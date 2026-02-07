"""
PANDORA - Sistema Especialista em Sobrevivência e Primeiros Socorros
Versão 3.0 - Inteligência Artificial Especializada
Baseado em: FM 3-05.70, SAS Survival Handbook, Red Cross First Aid Manual, Army Field Manual 21-76
"""

import random
import numpy as np
import gymnasium as gym
from gymnasium import spaces
from typing import Tuple, Dict, Any, Optional, List, Callable
from enum import Enum, IntEnum
import logging
from dataclasses import dataclass, field
import json
from datetime import datetime
from collections import deque, defaultdict
import hashlib
import re
import textwrap

# Configuração de logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger("PANDORA")

# ============================================================================
# MANUAL COMPLETO DE PRIMEIROS SOCORROS - EXPANDIDO
# ============================================================================

class FirstAidManual:
    """Manual completo de primeiros socorros baseado em protocolos internacionais"""
    
    # Seção 1: Protocolo ABCDE (Avaliação Primária)
    ABCDE_PROTOCOL = {
        'A': {
            'name': 'Airway (Via Aérea)',
            'steps': [
                'Verificar obstruções',
                'Manter coluna cervical alinhada',
                'Inclinação da cabeça e elevação do queixo',
                'Aspirar secreções se necessário'
            ],
            'signs_of_problem': [
                'Respiração ruidosa',
                'Ronco, estridor, gorgolejo',
                'Cianose (lábios azulados)'
            ]
        },
        'B': {
            'name': 'Breathing (Respiração)',
            'steps': [
                'Olhar, Ouvir e Sentir por 10 segundos',
                'Verificar frequência respiratória (normal: 12-20/min)',
                'Avaliar profundidade e ritmo',
                'Verificar uso de músculos acessórios'
            ],
            'emergency_measures': [
                'Ventilação de resgate se não respira',
                'Oxigênio suplementar se disponível'
            ]
        },
        'C': {
            'name': 'Circulation (Circulação)',
            'steps': [
                'Verificar pulso carotídeo por 10 segundos',
                'Avaliar perfusão (tempo de enchimento capilar <2s)',
                'Verificar sangramentos ativos',
                'Monitorar pressão arterial se possível'
            ],
            'critical_signs': [
                'Pulso ausente ou fraco',
                'Pele pálida, úmida e fria',
                'Sangramento arterial (jatos pulsáteis)'
            ]
        },
        'D': {
            'name': 'Disability (Deficiência Neurológica)',
            'steps': [
                'Avaliar nível de consciência (AVPU)',
                'Verificar pupilas (tamanho, reação, igualdade)',
                'Avaliar função motora (movimento dos membros)',
                'Verificar sensibilidade'
            ],
            'avpu_scale': {
                'A': 'Alert (Alerta)',
                'V': 'Voice (Responde à voz)',
                'P': 'Pain (Responde à dor)',
                'U': 'Unresponsive (Não responsivo)'
            }
        },
        'E': {
            'name': 'Exposure/Environment (Exposição/Ambiente)',
            'steps': [
                'Examinar completamente o corpo',
                'Manter temperatura corporal',
                'Proteger do ambiente',
                'Prevenir hipotermia/hipertermia'
            ]
        }
    }
    
    # Seção 2: Controle de Hemorragias
    BLEEDING_CONTROL = {
        'DIRECT_PRESSURE': {
            'technique': 'Pressão direta sobre o ferimento',
            'steps': [
                'Usar luvas ou barreira se disponível',
                'Aplicar gaze estéril ou pano limpo',
                'Manter pressão constante por 10-15 minutos',
                'Não remover gaze - adicionar mais se encharcar'
            ],
            'indications': 'Sangramento leve a moderado'
        },
        'ELEVATION': {
            'technique': 'Elevação do membro',
            'steps': [
                'Elevar ferimento acima do nível do coração',
                'Combinar com pressão direta',
                'Manter posição elevada'
            ],
            'indications': 'Sangramento em extremidades'
        },
        'PRESSURE_POINTS': {
            'technique': 'Pontos de pressão arterial',
            'points': {
                'brachial': 'Parte interna do braço (sangramento braquial)',
                'femoral': 'Virilha (sangramento femoral)'
            },
            'warning': 'Não usar por mais de 10 minutos'
        },
        'TOURNIQUET': {
            'technique': 'Torniquete - ÚLTIMO RECURSO',
            'steps': [
                'Aplicar 2-3 polegadas acima do ferimento (não sobre articulação)',
                'Apertar até parar sangramento',
                'Registrar hora de aplicação',
                'NÃO REMOVER - apenas profissional médico remove'
            ],
            'indications': 'Sangramento arterial incontrolável, amputação traumática'
        }
    }
    
    # Seção 3: Queimaduras
    BURN_TREATMENT = {
        'FIRST_DEGREE': {
            'description': 'Queimadura superficial (apenas epiderme)',
            'signs': ['Vermelhidão', 'Dor leve', 'Sem bolhas'],
            'treatment': [
                'Resfriar com água corrente por 10-20 minutos',
                'Aplicar pomada para queimaduras',
                'Cobrir com gaze não aderente',
                'Analgésicos se necessário'
            ]
        },
        'SECOND_DEGREE': {
            'description': 'Queimadura de espessura parcial (derme)',
            'signs': ['Bolhas', 'Vermelhidão intensa', 'Dor severa', 'Inchaço'],
            'treatment': [
                'NÃO ROMPER BOLHAS',
                'Resfriar com água corrente',
                'Aplicar pomada antibacteriana',
                'Cobrir com gaze estéril não aderente',
                'Procurar atendimento médico'
            ]
        },
        'THIRD_DEGREE': {
            'description': 'Queimadura de espessura total (todas camadas)',
            'signs': ['Pele branca ou carbonizada', 'Indolor (nervos destruídos)', 'Textura de couro'],
            'treatment': [
                'NÃO RESFRIAR (risco de hipotermia)',
                'Cobrir com pano limpo e seco',
                'NÃO REMOVER ROUPA ADERIDA',
                'NÃO APLICAR POMADAS',
                'TRANSPORTE URGENTE para hospital'
            ]
        },
        'CHEMICAL_BURNS': {
            'special_protocol': [
                'Remover roupa contaminada',
                'Lavar com água corrente por 20-30 minutos',
                'NÃO NEUTRALIZAR QUÍMICOS',
                'Identificar agente químico se possível'
            ]
        }
    }
    
    # Seção 4: Fraturas e Imobilizações
    FRACTURE_MANAGEMENT = {
        'SIGNS_OF_FRACTURE': [
            'Dor intensa no local',
            'Inchaço e deformidade',
            'Incapacidade de mover o membro',
            'Crepitação (som de ossos quebrando)',
            'Ferimento aberto (fratura exposta)'
        ],
        'SPLINTING_RULES': {
            'rule_1': 'Imitar o membro saudável para imobilização',
            'rule_2': 'Imobilizar articulação acima e abaixo da fratura',
            'rule_3': 'Verificar circulação, movimento e sensibilidade antes e depois',
            'rule_4': 'Usar materiais rígidos (galhos, tábuas, jornais enrolados)'
        },
        'SPECIFIC_FRACTURES': {
            'CLAVICLE': {
                'immobilization': 'Tipoia e imobilização em 8',
                'materials': 'Tipoia improvisada com camiseta, faixa em 8 com tiras de pano'
            },
            'ARM': {
                'immobilization': 'Tipoia e imobilização',
                'position': 'Cotovelo a 90°, palma contra o peito'
            },
            'LEG': {
                'immobilization': 'Tala longa',
                'materials': 'Galhos, tábuas, cobertores enrolados',
                'warning': 'Nunca tentar realinhar fratura de fêmur'
            }
        },
        'OPEN_FRACTURE_PROTOCOL': [
            'Cobrir ferimento com gaze estéril úmida',
            'NÃO recolocar osso exposto',
            'Imobilizar na posição encontrada',
            'Procurar atendimento urgente'
        ]
    }
    
    # Seção 5: Choque
    SHOCK_TREATMENT = {
        'TYPES': {
            'HYPOVOLEMIC': 'Perda de volume sanguíneo (hemorragia)',
            'CARDIOGENIC': 'Falha cardíaca',
            'NEUROGENIC': 'Lesão medular',
            'ANAPHYLACTIC': 'Reação alérgica severa',
            'SEPTIC': 'Infecção generalizada'
        },
        'SIGNS_AND_SYMPTOMS': [
            'Pele pálida, úmida e fria',
            'Pulso rápido e fraco (>100 bpm)',
            'Respiração rápido e superficial',
            'Náusea e vômito',
            'Agitação seguida de letargia',
            'Pressão arterial baixa (tardia)'
        ],
        'TREATMENT_PROTOCOL': [
            'Manter ABCs',
            'Controlar sangramentos',
            'Manter temperatura corporal',
            'Elevar pernas 30-45cm (exceto trauma craniano ou fraturas)',
            'NÃO dar líquidos ou comida',
            'Transporte urgente'
        ]
    }
    
    # Seção 6: Hipotermia e Hipertermia
    TEMPERATURE_EMERGENCIES = {
        'HYPOTHERMIA': {
            'MILD (32-35°C)': {
                'signs': ['Tremores intensos', 'Fala arrastada', 'Descoordenação'],
                'treatment': [
                    'Remover roupas molhadas',
                    'Aquecimento passivo (cobertores)',
                    'Bebidas quentes e doces (se consciente)',
                    'Aquecer gradualmente'
                ]
            },
            'MODERATE (28-32°C)': {
                'signs': ['Tremores cessam', 'Consciência diminuída', 'Bradicardia'],
                'treatment': [
                    'Aquecimento ativo externo (tronco primeiro)',
                    'NÃO massagear extremidades',
                    'Transporte urgente',
                    'Manipulação mínima'
                ]
            },
            'SEVERE (<28°C)': {
                'signs': ['Inconsciente', 'Rigidez muscular', 'Pulso fraco ou ausente'],
                'treatment': [
                    'Manipulação EXTREMAMENTE GENTIL',
                    'Aquecimento ativo interno (hospital)',
                    'RCP se sem pulso (compressões mais lentas)',
                    'Transporte URGENTÍSSIMO'
                ]
            }
        },
        'HYPERTHERMIA': {
            'HEAT_EXHAUSTION': {
                'signs': ['Sudorese profusa', 'Fraqueza', 'Náusea', 'Cãibras'],
                'treatment': [
                    'Repouso em lugar fresco',
                    'Hidratação oral',
                    'Compressas frias',
                    'Elevar pernas'
                ]
            },
            'HEAT_STROKE': {
                'signs': ['Pele quente e seca', 'Confusão/agressão', 'Convulsões', 'Coma'],
                'treatment': [
                    'RESFRIAMENTO IMEDIATO',
                    'Imersão em água fria',
                    'Ventilação e compressas frias',
                    'TRANSPORTE URGENTE',
                    'NÃO dar medicamentos'
                ]
            }
        }
    }
    
    # Seção 7: Intoxicações
    POISONING_TREATMENT = {
        'GENERAL_PROTOCOL': [
            'Afaste a vítima da fonte',
            'Identifique a substância se seguro',
            'Ligue para centro de toxicologia',
            'NÃO induza vômito (exceto sob orientação)',
            'Leve a embalagem/restos ao hospital'
        ],
        'SPECIFIC_ANTIDOTES': {
            'OPIOIDS': 'Naloxona (se disponível)',
            'ORGANOPHOSPHATES': 'Atropina',
            'CYANIDE': 'Kit de cianeto (amyl nitrite)',
            'CARBON_MONOXIDE': 'Oxigênio a 100%'
        }
    }
    
    # Seção 8: Emergências Cardíacas
    CARDIAC_EMERGENCIES = {
        'HEART_ATTACK_SIGNS': [
            'Dor no peito (aperto/peso)',
            'Dor irradiando para braço esquerdo/pescoço/mandíbula',
            'Falta de ar',
            'Suor frio',
            'Náusea'
        ],
        'CARDIAC_ARREST_PROTOCOL': [
            'Verificar segurança da cena',
            'Verificar responsividade',
            'Chamar ajuda/suporte avançado',
            'Iniciar RCP imediatamente',
            'Usar DEA se disponível'
        ],
        'CPR_GUIDELINES': {
            'ADULT': '30 compressões : 2 ventilações, 5-6cm profundidade, 100-120/min',
            'CHILD': '30:2 ou 15:2 se 2 socorristas, 5cm profundidade',
            'INFANT': '30:2 ou 15:2, 4cm profundidade, 2 dedos abaixo linha mamilar'
        }
    }
    
    @classmethod
    def get_emergency_protocol(cls, emergency_type: str) -> Dict:
        """Retorna protocolo para tipo específico de emergência"""
        protocols = {
            'bleeding': cls.BLEEDING_CONTROL,
            'burn': cls.BURN_TREATMENT,
            'fracture': cls.FRACTURE_MANAGEMENT,
            'shock': cls.SHOCK_TREATMENT,
            'hypothermia': cls.TEMPERATURE_EMERGENCIES['HYPOTHERMIA'],
            'heat_stroke': cls.TEMPERATURE_EMERGENCIES['HYPERTHERMIA']['HEAT_STROKE'],
            'heart_attack': cls.CARDIAC_EMERGENCIES,
            'poisoning': cls.POISONING_TREATMENT
        }
        return protocols.get(emergency_type.lower(), {})

# ============================================================================
# MANUAL COMPLETO DE SOBREVIVÊNCIA
# ============================================================================

class SurvivalManual:
    """Manual completo de sobrevivência baseado em técnicas militares"""
    
    # Prioridades de Sobrevivência (Regra dos 3)
    SURVIVAL_PRIORITIES = {
        'AIR': '3 minutos sem ar',
        'BODY_TEMP': '3 horas sem regulação térmica',
        'WATER': '3 dias sem água',
        'FOOD': '3 semanas sem comida',
        'HOPE': '3 meses sem esperança'
    }
    
    # Técnicas de Água
    WATER_TECHNIQUES = {
        'SOLAR_STILL': {
            'description': 'Destilador solar para ambientes desérticos',
            'materials': ['Plástico transparente', 'Recipiente', 'Tubo (opcional)', 'Pedra pequena'],
            'steps': [
                'Cavar buraco no solo (diâmetro 1m, profundidade 60cm)',
                'Colocar recipiente no centro',
                'Colocar tubo do recipiente para fora do buraco',
                'Cobrir com plástico e selar bordas com areia/terra',
                'Colocar pedra no centro para formar cone',
                'A água condensará no plástico e escorrerá para o recipiente'
            ],
            'yield': '0.5-1 litro/dia',
            'time': '4-6 horas para primeira coleta'
        },
        'TRANSPIRATION_BAG': {
            'description': 'Coleta de água por transpiração vegetal',
            'materials': ['Saco plástico transparente', 'Galho com folhas', 'Corda/pedra'],
            'steps': [
                'Escolher galho saudável com folhas (não tóxicas)',
                'Cobrir galho com saco plástico',
                'Amarrar abertura do saco firmemente',
                'Posicionar para que condensação escorra para baixo',
                'A água será liberada pela transpiração das folhas'
            ],
            'yield': '100-300ml por galho/dia',
            'warning': 'Evitar plantas tóxicas (seiva leitosa, espinhos)'
        }
    }
    
    # Técnicas de Fogo
    FIRE_TECHNIQUES = {
        'BOW_DRILL': {
            'description': 'Método de arco e fuso para fogo por fricção',
            'materials': [
                'Base: madeira macia (cedro, salgueiro)',
                'Fuso: madeira dura (carvalho, bordo)',
                'Arco: galho curvo com corda',
                'Camisa: folha, casca ou couro',
                'Ninho: material inflamável seco (grama, casca)'
            ],
            'steps': [
                'Preparar base com entalhe em V',
                'Colocar camisa sob entalhe',
                'Posicionar fuso no entalhe',
                'Mover arco para frente e trás para girar fuso',
                'Coletar brasa na camisa',
                'Transferir brasa para ninho e assoprar suavemente'
            ],
            'difficulty': 'Alta',
            'time': '10-30 minutos para iniciantes'
        }
    }
    
    # Plantas Comestíveis (Regras Gerais)
    EDIBLE_PLANTS_RULES = {
        'UNIVERSAL_EDIBILITY_TEST': [
            'Separe planta em partes (folhas, caule, raiz, fruto)',
            'Teste cada parte separadamente',
            'Esfregue parte na pele interna do pulso e espere 15min',
            'Coloque pequena quantidade nos lábios, espere 3min',
            'Mastigue pequeno pedaço sem engolir, espere 15min',
            'Se nenhuma reação, engula pequena quantidade e espere 8h',
            'Se sem sintomas, planta é segura em pequenas quantidades'
        ],
        'DANGER_SIGNS': [
            'Seiva leitosa ou colorida',
            'Cheiro de amêndoas amargas (cianeto)',
            'Grãos com ponta preta/rosa (fungos alucinógenos)',
            'Plantas com espinhos finos como cabelos',
            'Folhas em grupos de três (evitar, muitas são tóxicas)'
        ]
    }
    
    @classmethod
    def get_survival_tip(cls, situation: str) -> str:
        """Retorna dica de sobrevivência para situação específica"""
        tips = {
            'lost': 'PARE: Pare, Pense, Observe, Planeje. Não entre em pânico.',
            'thirsty': 'Não coma se não tiver água - digestão consome água',
            'cold': 'Isolação > Fogo. Construa abrigo antes de tentar fogo',
            'hungry': 'Insetos são proteína mais segura que plantas desconhecidas',
            'injured': 'Priorize sangramentos > fraturas > outros ferimentos'
        }
        return tips.get(situation.lower(), "Mantenha a calma e avalie a situação")

# ============================================================================
# PANDORA - ESPECIALISTA EM SOBREVIVÊNCIA
# ============================================================================

class PANDORA:
    """
    Sistema especialista PANDORA - Combina conhecimentos de:
    1. Primeiros Socorros
    2. Sobrevivência Militar
    3. Medicina de Emergência
    4. Técnicas de Resgate
    """
    
    def __init__(self):
        self.first_aid_manual = FirstAidManual()
        self.survival_manual = SurvivalManual()
        self.conversation_history = deque(maxlen=50)
        self.user_context = {}
        
    def analyze_situation(self, symptoms: List[str], context: Dict) -> Dict:
        """Analisa situação e fornece diagnóstico e tratamento"""
        analysis = {
            'emergency_level': 'low',
            'probable_causes': [],
            'immediate_actions': [],
            'monitoring_signs': [],
            'warnings': []
        }
        
        # Mapeamento de sintomas para condições
        symptom_mapping = {
            'bleeding': ['sangramento', 'sangra', 'cortou', 'corte', 'ferido'],
            'burn': ['queimou', 'queimadura', 'queimado', 'fogo'],
            'fracture': ['quebrou', 'osso', 'fratura', 'inchaço', 'deformidade'],
            'unconscious': ['inconsciente', 'desmaiou', 'não responde'],
            'difficulty_breathing': ['falta de ar', 'respiração difícil', 'chiado'],
            'chest_pain': ['dor no peito', 'aperto no peito', 'dor no braço esquerdo'],
            'hypothermia': ['frio', 'tremendo', 'pele fria', 'confusão', 'sonolência'],
            'heat_stroke': ['calor', 'pele quente', 'sem suor', 'confusão', 'convulsão']
        }
        
        # Identifica condições baseado nos sintomas
        detected_conditions = []
        symptoms_lower = [s.lower() for s in symptoms]
        
        for condition, keywords in symptom_mapping.items():
            if any(keyword in symptom for symptom in symptoms_lower for keyword in keywords):
                detected_conditions.append(condition)
        
        # Define nível de emergência
        critical_conditions = ['unconscious', 'difficulty_breathing', 'chest_pain', 'severe_bleeding']
        if any(cond in detected_conditions for cond in critical_conditions):
            analysis['emergency_level'] = 'critical'
        elif detected_conditions:
            analysis['emergency_level'] = 'medium'
        
        analysis['probable_causes'] = detected_conditions
        
        # Adiciona ações específicas
        if 'bleeding' in detected_conditions:
            analysis['immediate_actions'].extend([
                "Aplicar pressão direta com pano limpo",
                "Elevar ferimento acima do coração",
                "Não remover gaze se encharcar - adicione mais"
            ])
            
        if 'unconscious' in detected_conditions:
            analysis['immediate_actions'].extend([
                "Verificar responsividade (AVPU)",
                "Verificar respiração",
                "Posicionar em recuperação se respirando",
                "Iniciar RCP se não respirar"
            ])
            analysis['emergency_level'] = 'critical'
        
        return analysis
    
    def get_first_aid_instructions(self, injury_type: str) -> str:
        """Retorna instruções detalhadas de primeiros socorros"""
        instructions = {
            'cut': self._format_bleeding_instructions(),
            'burn': self._format_burn_instructions(),
            'fracture': self._format_fracture_instructions(),
            'choking': self._format_choking_instructions(),
            'shock': self._format_shock_instructions(),
            'hypothermia': self._format_hypothermia_instructions(),
            'heat_stroke': self._format_heat_stroke_instructions()
        }
        
        return instructions.get(injury_type.lower(), self._format_general_first_aid())
    
    def _format_bleeding_instructions(self) -> str:
        """Formata instruções para controle de hemorragia"""
        protocol = self.first_aid_manual.BLEEDING_CONTROL
        text = "🚨 CONTROLE DE HEMORRAGIA - PROTOCOLO:\n\n"
        
        for method, details in protocol.items():
            text += f"🔹 {details['technique']}:\n"
            if 'steps' in details:
                for step in details['steps']:
                    text += f"   • {step}\n"
            text += "\n"
        
        text += "⚠️ LEMBRETE: Torniquete é ÚLTIMO RECURSO. Registrar hora da aplicação."
        return text
    
    def _format_burn_instructions(self) -> str:
        """Formata instruções para queimaduras"""
        burns = self.first_aid_manual.BURN_TREATMENT
        text = "🔥 TRATAMENTO DE QUEIMADURAS:\n\n"
        
        for degree, details in burns.items():
            if degree.startswith(('FIRST', 'SECOND', 'THIRD')):
                text += f"📌 {degree.replace('_', ' ').title()}:\n"
                text += f"   Sinais: {', '.join(details['signs'])}\n"
                text += "   Tratamento:\n"
                for step in details['treatment']:
                    text += f"   • {step}\n"
                text += "\n"
        
        return text
    
    def _format_fracture_instructions(self) -> str:
        """Formata instruções para fraturas"""
        fractures = self.first_aid_manual.FRACTURE_MANAGEMENT
        text = "🦴 MANEJO DE FRATURAS:\n\n"
        
        text += "📌 Sinais de fratura:\n"
        for sign in fractures['SIGNS_OF_FRACTURE']:
            text += f"   • {sign}\n"
        
        text += "\n📌 Regras de imobilização:\n"
        for key, rule in fractures['SPLINTING_RULES'].items():
            text += f"   • {rule}\n"
        
        text += "\n⚠️ FRATURA EXPOSTA: Cobrir com gaze úmida, NÃO recolocar osso!"
        return text
    
    def _format_choking_instructions(self) -> str:
        """Formata instruções para engasgamento"""
        text = "🤢 MANOBRA DE HEIMLICH (ENGASGAMENTO):\n\n"
        
        text += "👤 ADULTO/CRIANÇA (consciente):\n"
        text += "   1. Posicione-se atrás da vítima\n"
        text += "   2. Envolva a cintura com os braços\n"
        text += "   3. Feche uma mão em punho acima do umbigo\n"
        text += "   4. Comprima para dentro e para cima\n"
        text += "   5. Repita até desobstruir ou vítima desmaiar\n\n"
        
        text += "👶 BEBÊ (menos de 1 ano):\n"
        text += "   1. Posicione bebê de bruços no seu antebraço\n"
        text += "   2. Dê 5 golpes firmes nas costas\n"
        text += "   3. Vire e dê 5 compressões torácicas\n"
        text += "   4. Repita até desobstruir\n\n"
        
        text += "⚠️ Se vítima desmaiar: Iniciar RCP imediatamente"
        return text
    
    def _format_shock_instructions(self) -> str:
        """Formata instruções para choque"""
        shock = self.first_aid_manual.SHOCK_TREATMENT
        text = "💢 TRATAMENTO DO CHOQUE:\n\n"
        
        text += "📌 Sinais e sintomas:\n"
        for symptom in shock['SIGNS_AND_SYMPTOMS']:
            text += f"   • {symptom}\n"
        
        text += "\n📌 Protocolo de tratamento:\n"
        for step in shock['TREATMENT_PROTOCOL']:
            text += f"   • {step}\n"
        
        return text
    
    def _format_hypothermia_instructions(self) -> str:
        """Formata instruções para hipotermia"""
        hypothermia = self.first_aid_manual.TEMPERATURE_EMERGENCIES['HYPOTHERMIA']
        text = "❄️ HIPOTERMIA - TRATAMENTO POR ESTÁGIO:\n\n"
        
        for stage, details in hypothermia.items():
            text += f"📌 {stage}:\n"
            text += f"   Sinais: {', '.join(details['signs'])}\n"
            text += "   Tratamento:\n"
            for step in details['treatment']:
                text += f"   • {step}\n"
            text += "\n"
        
        text += "⚠️ Hipotermia severa: Manipulação EXTREMAMENTE GENTIL!"
        return text
    
    def _format_heat_stroke_instructions(self) -> str:
        """Formata instruções para insolação"""
        heat_stroke = self.first_aid_manual.TEMPERATURE_EMERGENCIES['HYPERTHERMIA']['HEAT_STROKE']
        text = "🌡️ INSOLAÇÃO (EMERGÊNCIA MÉDICA):\n\n"
        
        text += "📌 Sinais:\n"
        for sign in heat_stroke['signs']:
            text += f"   • {sign}\n"
        
        text += "\n📌 Tratamento (RESFRIAMENTO IMEDIATO):\n"
        for step in heat_stroke['treatment']:
            text += f"   • {step}\n"
        
        text += "\n⏰ TEMPO É CÉREBRO: Resfriar antes de transportar!"
        return text
    
    def _format_general_first_aid(self) -> str:
        """Instruções gerais de primeiros socorros"""
        text = "🆘 PROTOCOLO GERAL DE PRIMEIROS SOCORROS:\n\n"
        
        text += "1. 🔒 SEGURANÇA: Verifique cena segura para você e vítima\n"
        text += "2. 📞 SOCORRO: Peça ajuda (192 SAMU, 193 Bombeiros)\n"
        text += "3. 👤 AVALIAÇÃO: Use protocolo ABCDE:\n"
        
        for letter, details in self.first_aid_manual.ABCDE_PROTOCOL.items():
            text += f"   {letter}. {details['name']}\n"
        
        text += "\n4. 💊 TRATAMENTO: Aplique técnicas apropriadas\n"
        text += "5. 🏥 TRANSPORTE: Prepare para transporte seguro\n"
        text += "\n⚠️ Lembre-se: Sua segurança vem em primeiro lugar!"
        
        return text
    
    def get_survival_advice(self, situation: str, environment: str = "forest") -> str:
        """Retorna conselhos de sobrevivência para situação específica"""
        advice = ""
        
        if situation == "water":
            technique = self.survival_manual.WATER_TECHNIQUES.get('SOLAR_STILL', {})
            advice = f"💧 TÉCNICA DE ÁGUA - DESTILADOR SOLAR:\n\n"
            advice += f"{technique.get('description', '')}\n\n"
            advice += "📦 Materiais necessários:\n"
            for material in technique.get('materials', []):
                advice += f"   • {material}\n"
            advice += "\n📝 Passos:\n"
            for i, step in enumerate(technique.get('steps', []), 1):
                advice += f"   {i}. {step}\n"
        
        elif situation == "fire":
            technique = self.survival_manual.FIRE_TECHNIQUES.get('BOW_DRILL', {})
            advice = f"🔥 TÉCNICA DE FOGO - ARCO E FUSO:\n\n"
            advice += f"Dificuldade: {technique.get('difficulty', 'Média')}\n"
            advice += f"Tempo estimado: {technique.get('time', '10-30min')}\n\n"
            advice += "🛠️ Materiais:\n"
            for material in technique.get('materials', []):
                advice += f"   • {material}\n"
        
        elif situation == "food":
            advice = "🍃 PLANTAS COMESTÍVEIS - TESTE UNIVERSAL:\n\n"
            advice += "⚠️ NUNCA coma planta desconhecida sem testar!\n\n"
            advice += "🧪 Teste de comestibilidade:\n"
            for i, step in enumerate(self.survival_manual.EDIBLE_PLANTS_RULES['UNIVERSAL_EDIBILITY_TEST'], 1):
                advice += f"   {i}. {step}\n"
            
            advice += "\n🚫 Sinais de perigo (EVITAR):\n"
            for sign in self.survival_manual.EDIBLE_PLANTS_RULES['DANGER_SIGNS']:
                advice += f"   • {sign}\n"
        
        elif situation == "shelter":
            advice = "🏠 CONSTRUÇÃO DE ABRIGO - LEAN-TO:\n\n"
            advice += "Materiais: Galhos, folhas, cordas\n"
            advice += "Tempo: 2-3 horas\n\n"
            advice += "Passos:\n"
            advice += "1. Encontre árvore com galho baixo ou dois troncos em V\n"
            advice += "2. Apoie galho longo (3-4m) no ponto de apoio\n"
            advice += "3. Adicione galhos menores em ângulo de 45°\n"
            advice += "4. Cubra com folhas grossas (30cm mínimo)\n"
            advice += "5. Adicione camada de terra/musgo para isolamento\n"
            advice += "6. Crie cama elevada com galhos e folhas\n"
        
        else:
            advice = self.survival_manual.get_survival_tip(situation)
        
        return advice
    
    def process_query(self, query: str) -> Dict:
        """Processa consulta do usuário e retorna resposta apropriada"""
        query_lower = query.lower()
        response = {
            'type': 'general',
            'content': '',
            'emergency': False,
            'suggested_actions': []
        }
        
        # Detecção de emergência
        emergency_keywords = [
            'socorro', 'emergência', 'ajuda', 'urgente', 'desmaio', 'sangrando',
            'infarto', 'ataque cardíaco', 'parou de respirar', 'engasgado'
        ]
        
        if any(keyword in query_lower for keyword in emergency_keywords):
            response['emergency'] = True
            response['content'] = "🚨 EMERGÊNCIA DETECTADA!\n\n"
            response['content'] += "1. Mantenha a calma\n"
            response['content'] += "2. Chame ajuda imediatamente (192/193)\n"
            response['content'] += "3. Siga as instruções abaixo\n"
        
        # Mapeamento de consultas para respostas
        if any(word in query_lower for word in ['primeiro socorro', 'primeiros socorros', 'ferimento']):
            response['type'] = 'first_aid'
            response['content'] = self._format_general_first_aid()
            
        elif any(word in query_lower for word in ['sangramento', 'sangra', 'corte']):
            response['type'] = 'first_aid'
            response['content'] = self._format_bleeding_instructions()
            
        elif any(word in query_lower for word in ['queimadura', 'queimou']):
            response['type'] = 'first_aid'
            response['content'] = self._format_burn_instructions()
            
        elif any(word in query_lower for word in ['fratura', 'osso quebrado', 'quebrou']):
            response['type'] = 'first_aid'
            response['content'] = self._format_fracture_instructions()
            
        elif any(word in query_lower for word in ['engasgado', 'engasgamento']):
            response['type'] = 'first_aid'
            response['content'] = self._format_choking_instructions()
            
        elif any(word in query_lower for word in ['choque', 'pálido', 'suor frio']):
            response['type'] = 'first_aid'
            response['content'] = self._format_shock_instructions()
            
        elif any(word in query_lower for word in ['frio', 'hipotermia', 'tremendo']):
            response['type'] = 'first_aid'
            response['content'] = self._format_hypothermia_instructions()
            
        elif any(word in query_lower for word in ['calor', 'insolação', 'hipertermia']):
            response['type'] = 'first_aid'
            response['content'] = self._format_heat_stroke_instructions()
            
        elif any(word in query_lower for word in ['água', 'sede', 'desidratação']):
            response['type'] = 'survival'
            response['content'] = self.get_survival_advice('water')
            
        elif any(word in query_lower for word in ['fogo', 'acender fogo', 'fazer fogo']):
            response['type'] = 'survival'
            response['content'] = self.get_survival_advice('fire')
            
        elif any(word in query_lower for word in ['comida', 'fome', 'planta comestível']):
            response['type'] = 'survival'
            response['content'] = self.get_survival_advice('food')
            
        elif any(word in query_lower for word in ['abrigo', 'abrigar', 'proteger']):
            response['type'] = 'survival'
            response['content'] = self.get_survival_advice('shelter')
            
        elif any(word in query_lower for word in ['perdido', 'sobrevivência', 'dica']):
            response['type'] = 'survival'
            response['content'] = self.survival_manual.get_survival_tip('lost')
            
        else:
            response['type'] = 'general'
            response['content'] = self._get_general_response()
        
        # Registra consulta no histórico
        self.conversation_history.append({
            'timestamp': datetime.now().isoformat(),
            'query': query,
            'response_type': response['type'],
            'emergency': response['emergency']
        })
        
        return response
    
    def _get_general_response(self) -> str:
        """Resposta geral quando não detecta consulta específica"""
        responses = [
            "👋 Olá! Sou PANDORA, especialista em sobrevivência e primeiros socorros.",
            "💡 PANDORA pode ajudar com: primeiros socorros, técnicas de sobrevivência, emergências médicas.",
            "🔍 Digite sua pergunta ou situação (ex: 'como tratar queimadura', 'como fazer fogo').",
            "🚨 Para emergências, digite SOCORRO ou EMERGÊNCIA imediatamente.",
            "\n📚 Tópicos disponíveis com PANDORA:",
            "   • 🩹 Primeiros Socorros (cortes, queimaduras, fraturas)",
            "   • 🔥 Sobrevivência (água, fogo, abrigo, comida)",
            "   • 🏥 Emergências (choque, hipotermia, insolação)",
            "   • 🧭 Técnicas militares de sobrevivência"
        ]
        return "\n".join(responses)
    
    def get_conversation_summary(self) -> str:
        """Retorna resumo da conversa atual"""
        if not self.conversation_history:
            return "PANDORA: Nenhuma conversa registrada ainda."
        
        summary = "📋 RESUMO DA CONVERSA COM PANDORA:\n\n"
        for i, entry in enumerate(self.conversation_history, 1):
            summary += f"{i}. [{entry['timestamp'][11:16]}] {entry['query'][:50]}...\n"
        
        return summary

# ============================================================================
# AMBIENTE DE SOBREVIVÊNCIA COM PANDORA INTEGRADA
# ============================================================================

class SurvivalEnvWithPANDORA(gym.Env):
    """Ambiente de sobrevivência com assistente PANDORA integrado"""
    
    metadata = {'render_modes': ['human', 'console', 'PANDORA'], 'render_fps': 4}
    
    def __init__(self, 
                 max_steps: int = 500,
                 difficulty: float = 1.0,
                 terrain: str = "forest",
                 enable_PANDORA: bool = True,
                 render_mode: str = 'human'):
        
        super().__init__()
        
        # Configuração
        self.max_steps = max_steps
        self.difficulty = max(0.1, difficulty)
        self.terrain = terrain
        self.render_mode = render_mode
        
        # Sistema PANDORA
        self.PANDORA = PANDORA() if enable_PANDORA else None
        self.PANDORA_active = False
        
        # Espaços de ação
        # Ações básicas: 0-11 + Ações PANDORA: 12-15
        self.action_space = spaces.Discrete(16)  # 16 ações totais
        
        # Espaço de observação
        self.observation_space = spaces.Box(
            low=np.array([0, 0, 0, 0, 35, 0, 0, 0, 0]),
            high=np.array([100, 100, 100, 100, 40, 365, 100, 100, 100]),
            dtype=np.float32
        )
        
        # Estado do jogador
        self.health = 100.0
        self.hunger = 50.0
        self.hydration = 50.0
        self.stamina = 80.0
        self.temperature = 37.0
        self.day = 1
        self.inventory = defaultdict(int)
        self.injuries = []
        self.skills = defaultdict(float)
        
        # Histórico
        self.steps = 0
        self.reward_history = []
        self.action_history = []
        
        logger.info(f"Ambiente SurvivalEnvWithPANDORA inicializado. Terreno: {terrain}")
    
    def reset(self, seed: Optional[int] = None, options: Optional[Dict] = None) -> Tuple[np.ndarray, Dict]:
        super().reset(seed=seed)
        
        # Reset estado
        self.health = 100.0
        self.hunger = 50.0
        self.hydration = 50.0
        self.stamina = 80.0
        self.temperature = 37.0
        self.day = 1
        self.inventory = defaultdict(int)
        self.injuries = []
        self.steps = 0
        self.reward_history = []
        self.action_history = []
        
        # Inventário inicial
        self.inventory.update({
            'food': random.randint(1, 3),
            'water': random.randint(2, 4),
            'materials': random.randint(2, 5),
            'medicine': random.randint(0, 2)
        })
        
        info = {
            'day': self.day,
            'health': self.health,
            'inventory': dict(self.inventory),
            'PANDORA_available': self.PANDORA is not None
        }
        
        return self._get_observation(), info
    
    def step(self, action: int) -> Tuple[np.ndarray, float, bool, bool, Dict]:
        self.steps += 1
        
        # Processa ação
        if action < 12:  # Ações de sobrevivência
            reward, action_info = self._process_survival_action(action)
        else:  # Ações PANDORA
            reward, action_info = self._process_PANDORA_action(action - 12)
        
        # Aplica efeitos ambientais
        self._apply_environmental_effects()
        
        # Calcula recompensa
        base_reward = self._calculate_base_reward()
        total_reward = reward + base_reward
        self.reward_history.append(total_reward)
        
        # Verifica término
        terminated, truncated = self._check_termination()
        
        # Avança dia
        if self.steps % 48 == 0:
            self.day += 1
        
        # Coleta informações
        info = self._collect_info(action_info)
        
        return self._get_observation(), total_reward, terminated, truncated, info
    
    def _process_survival_action(self, action: int) -> Tuple[float, Dict]:
        """Processa ação de sobrevivência básica"""
        action_info = {'type': 'survival', 'message': ''}
        reward = 0.0
        
        # Mapeamento simples de ações
        if action == 0:  # Buscar água
            if self.stamina > 15:
                water_found = random.randint(10, 30)
                self.hydration = min(100, self.hydration + water_found)
                self.inventory['water'] += 1
                self.stamina -= 15
                reward = 0.2
                action_info['message'] = f"Encontrou água (+{water_found} hidratação)"
        
        elif action == 1:  # Caçar
            if self.stamina > 25:
                if random.random() > 0.4:
                    food_gained = random.randint(15, 35)
                    self.hunger = max(0, self.hunger - food_gained)
                    self.inventory['food'] += 1
                    reward = 0.3
                    action_info['message'] = f"Caça bem-sucedida (-{food_gained} fome)"
                else:
                    self.stamina -= 10
                    reward = -0.1
                    action_info['message'] = "Caça falhou"
                self.stamina -= 25
        
        elif action == 2:  # Fazer fogo
            if self.inventory['materials'] > 0:
                self.temperature = min(40, self.temperature + 2.0)
                self.health = min(100, self.health + 10)
                self.inventory['materials'] -= 1
                reward = 0.15
                action_info['message'] = "Fogo aceso (+10 saúde, +2°C)"
        
        elif action == 3:  # Construir abrigo
            if self.inventory['materials'] >= 3:
                self.health = min(100, self.health + 20)
                self.inventory['materials'] -= 3
                self.stamina -= 30
                reward = 0.25
                action_info['message'] = "Abrigo construído (+20 saúde)"
        
        elif action == 4:  # Descansar
            rest_efficiency = min(1.0, self.hydration / 100)
            self.health = min(100, self.health + 20 * rest_efficiency)
            self.stamina = min(100, self.stamina + 40 * rest_efficiency)
            self.hunger += 10
            reward = 0.1
            action_info['message'] = f"Descanso ({rest_efficiency*100:.0f}% eficiência)"
        
        elif action == 5:  # Explorar
            if self.stamina > 20:
                self.stamina -= 25
                if random.random() > 0.5:
                    found = random.choice(['materials', 'food', 'medicine'])
                    self.inventory[found] += random.randint(1, 3)
                    reward = 0.15
                    action_info['message'] = f"Exploração bem-sucedida: encontrou {found}"
        
        elif action == 6:  # Primeiros socorros
            if self.inventory['medicine'] > 0 and self.health < 90:
                self.health = min(100, self.health + 25)
                self.inventory['medicine'] -= 1
                reward = 0.2
                action_info['message'] = "Primeiros socorros aplicados (+25 saúde)"
        
        return reward, action_info
    
    def _process_PANDORA_action(self, PANDORA_action: int) -> Tuple[float, Dict]:
        """Processa ação relacionada à PANDORA"""
        if not self.PANDORA:
            return -0.1, {'type': 'PANDORA', 'message': 'PANDORA não disponível'}
        
        action_info = {'type': 'PANDORA', 'message': '', 'PANDORA_response': ''}
        reward = 0.0
        
        # Simula consulta baseada na ação
        queries = [
            "primeiros socorros básicos",
            "como tratar corte",
            "como fazer fogo",
            "encontre água no deserto"
        ]
        
        if PANDORA_action < len(queries):
            query = queries[PANDORA_action]
            response = self.PANDORA.process_query(query)
            action_info['PANDORA_response'] = response['content']
            action_info['message'] = f"Consultou PANDORA: {query}"
            reward = 0.05  # Recompensa por buscar conhecimento
        
        return reward, action_info
    
    def _apply_environmental_effects(self):
        """Aplica efeitos ambientais"""
        # Fome e sede aumentam
        self.hunger = min(100, self.hunger + 2 * self.difficulty)
        self.hydration = max(0, self.hydration - 1.5 * self.difficulty)
        
        # Efeitos da fome
        if self.hunger > 80:
            self.health -= 5
        elif self.hunger > 60:
            self.health -= 2
        
        # Efeitos da sede
        if self.hydration < 20:
            self.health -= 8
        elif self.hydration < 40:
            self.health -= 3
        
        # Temperatura varia aleatoriamente
        self.temperature += random.uniform(-0.5, 0.5)
        self.temperature = np.clip(self.temperature, 35, 40)
        
        # Regeneração natural
        if self.hunger < 40 and self.hydration > 60:
            self.health = min(100, self.health + 1)
            self.stamina = min(100, self.stamina + 2)
        
        # Normaliza valores
        self.health = np.clip(self.health, 0, 100)
        self.hunger = np.clip(self.hunger, 0, 100)
        self.hydration = np.clip(self.hydration, 0, 100)
        self.stamina = np.clip(self.stamina, 0, 100)
    
    def _calculate_base_reward(self) -> float:
        """Calcula recompensa base de sobrevivência"""
        reward = self.health / 200.0  # Máximo 0.5
        reward -= self.hunger / 200.0
        reward -= (100 - self.hydration) / 200.0
        
        # Bônus por recursos
        resource_bonus = sum(self.inventory.values()) / 100.0
        reward += resource_bonus * 0.1
        
        # Bônus por sobrevivência diária
        reward += self.day * 0.01
        
        return reward
    
    def _check_termination(self) -> Tuple[bool, bool]:
        """Verifica condições de término"""
        terminated = False
        truncated = False
        
        if self.health <= 0:
            terminated = True
        elif self.steps >= self.max_steps:
            truncated = True
        elif self.day >= 30:
            terminated = True  # Sobreviveu 30 dias
        
        return terminated, truncated
    
    def _get_observation(self) -> np.ndarray:
        """Retorna observação do estado"""
        return np.array([
            self.health / 100.0,
            self.hunger / 100.0,
            self.hydration / 100.0,
            self.stamina / 100.0,
            (self.temperature - 35) / 5.0,
            self.day / 365.0,
            sum(self.inventory.values()) / 50.0,
            len(self.injuries) / 10.0,
            1.0 if self.PANDORA_active else 0.0
        ], dtype=np.float32)
    
    def _collect_info(self, action_info: Dict) -> Dict:
        """Coleta informações do passo"""
        info = {
            'day': self.day,
            'health': self.health,
            'hunger': self.hunger,
            'hydration': self.hydration,
            'stamina': self.stamina,
            'temperature': self.temperature,
            'inventory': dict(self.inventory),
            'action_info': action_info
        }
        
        if 'PANDORA_response' in action_info:
            info['PANDORA_response'] = action_info['PANDORA_response']
        
        return info
    
    def render(self):
        """Renderiza o estado"""
        if self.render_mode == 'human':
            print("\n" + "="*70)
            print("SOBREVIVÊNCIA COM PANDORA")
            print("="*70)
            print(f"Dia: {self.day:3d} | Passo: {self.steps:3d}")
            print(f"Saúde: {self.health:5.1f}/100 | Fome: {self.hunger:5.1f}/100")
            print(f"Sede:  {self.hydration:5.1f}/100 | Energia: {self.stamina:5.1f}/100")
            print(f"Temp:  {self.temperature:5.1f}°C")
            print("-"*70)
            print("INVENTÁRIO:")
            for item, qty in self.inventory.items():
                if qty > 0:
                    print(f"  {item:10}: {qty:3d}")
            print("="*70)
        
        elif self.render_mode == 'PANDORA' and self.PANDORA:
            # Modo especial para mostrar informações da PANDORA
            summary = self.PANDORA.get_conversation_summary()
            print("\n" + "="*70)
            print("PANDORA - ESPECIALISTA EM SOBREVIVÊNCIA")
            print("="*70)
            print(summary)
            print("="*70)
    
    def consult_PANDORA(self, query: str) -> str:
        """Consulta a PANDORA diretamente"""
        if not self.PANDORA:
            return "PANDORA não está disponível neste ambiente."
        
        response = self.PANDORA.process_query(query)
        
        # Ativa PANDORA se for emergência
        if response['emergency']:
            self.PANDORA_active = True
        
        return response['content']
    
    def get_PANDORA_tip(self) -> str:
        """Obtém dica da PANDORA baseada no estado atual"""
        if not self.PANDORA:
            return ""
        
        # Dica baseada no estado
        if self.hydration < 30:
            return self.PANDORA.get_survival_advice('water')
        elif self.hunger > 70:
            return self.PANDORA.get_survival_advice('food')
        elif self.temperature < 36:
            return self.PANDORA.get_first_aid_instructions('hypothermia')
        elif self.health < 50:
            return self.PANDORA._format_general_first_aid()
        else:
            return self.PANDORA.survival_manual.get_survival_tip('lost')
    
    def close(self):
        """Fecha o ambiente"""
        logger.info("Ambiente SurvivalEnvWithPANDORA fechado.")

# ============================================================================
# INTERFACE DE CONVERSAÇÃO COM PANDORA
# ============================================================================

class PANDORAChatInterface:
    """Interface de conversação com PANDORA"""
    
    def __init__(self):
        self.PANDORA = PANDORA()
        self.conversation_active = True
        self.user_context = {}
        
    def start_conversation(self):
        """Inicia conversação com PANDORA"""
        print("\n" + "="*80)
        print("PANDORA - SISTEMA ESPECIALISTA EM SOBREVIVÊNCIA E PRIMEIROS SOCORROS")
        print("="*80)
        print("\n💡 Digite 'ajuda' para ver comandos disponíveis")
        print("💡 Digite 'sair' para encerrar")
        print("🚨 Digite 'emergência' para protocolos de emergência")
        print("="*80)
        
        while self.conversation_active:
            try:
                user_input = input("\nVocê: ").strip()
                
                if not user_input:
                    continue
                
                if user_input.lower() in ['sair', 'exit', 'quit']:
                    print("\nPANDORA: Até logo! Lembre-se: conhecimento salva vidas. 🫡")
                    self.conversation_active = False
                    break
                
                elif user_input.lower() in ['ajuda', 'help', '?']:
                    self._show_help()
                
                elif user_input.lower() in ['emergencia', 'emergência', 'socorro']:
                    self._handle_emergency()
                
                elif user_input.lower() in ['resumo', 'histórico']:
                    summary = self.PANDORA.get_conversation_summary()
                    print(f"\nPANDORA: {summary}")
                
                elif user_input.lower() in ['limpar', 'clear']:
                    self.PANDORA.conversation_history.clear()
                    print("\nPANDORA: Histórico limpo.")
                
                else:
                    response = self.PANDORA.process_query(user_input)
                    self._display_response(response)
                    
            except KeyboardInterrupt:
                print("\n\nPANDORA: Conversa interrompida. Até logo!")
                break
            except Exception as e:
                print(f"\nPANDORA: Ocorreu um erro. {e}")
    
    def _show_help(self):
        """Mostra ajuda dos comandos"""
        help_text = """
📚 COMANDOS DISPONÍVEIS COM PANDORA:

🆘 EMERGÊNCIAS:
  • 'emergência' - Protocolo de emergência
  • 'sangramento' - Controle de hemorragia
  • 'queimadura' - Tratamento de queimaduras
  • 'fratura' - Manejo de fraturas
  • 'engasgamento' - Manobra de Heimlich
  • 'choque' - Tratamento do choque
  • 'hipotermia' - Tratamento de hipotermia
  • 'insolação' - Tratamento de insolação

🏕️ SOBREVIVÊNCIA:
  • 'água' - Técnicas para encontrar água
  • 'fogo' - Como fazer fogo
  • 'comida' - Plantas comestíveis
  • 'abrigo' - Construção de abrigo
  • 'perdido' - O que fazer se perdido

💬 GERAIS:
  • 'primeiros socorros' - Protocolo geral
  • 'resumo' - Ver histórico da conversa com PANDORA
  • 'limpar' - Limpar histórico do PANDORA
  • 'sair' - Encerrar conversa com PANDORA

💡 EXEMPLOS:
  • "Como tratar um corte profundo?"
  • "Estou no deserto, como encontrar água?"
  • "Meu amigo desmaiou, o que fazer?"
  • "Como fazer fogo sem fósforos?"
        """
        print(help_text)
    
    def _handle_emergency(self):
        """Manipula situação de emergência"""
        print("\n" + "🚨" * 40)
        print("EMERGÊNCIA DETECTADA!")
        print("🚨" * 40)
        
        emergency_protocol = """
1. ⚠️  MANTENHA A CALMA - Não entre em pânico
2. 📞  CHAME AJUDA - Disque 192 (SAMU) ou 193 (Bombeiros)
3. 🔒  VERIFIQUE SEGURANÇA - Cena segura para você e vítima
4. 👤  AVALIE VÍTIMA - Use protocolo ABCDE:
   A - Via aérea (Airway)
   B - Respiração (Breathing)
   C - Circulação (Circulation)
   D - Deficiência neurológica (Disability)
   E - Exposição (Exposure)
5. 💊  APLIQUE PRIMEIROS SOCORROS
6. 🏥  AGUARDE/PREPARE TRANSPORTE

Descreva a situação para instruções específicas do PANDORA:
"""
        print(emergency_protocol)
    
    def _display_response(self, response: Dict):
        """Exibe resposta formatada da PANDORA"""
        if response['emergency']:
            print("\n" + "🚨" * 40)
            print("RESPOSTA DE EMERGÊNCIA DO PANDORA:")
            print("🚨" * 40)
        
        # Divide resposta em linhas para melhor formatação
        lines = response['content'].split('\n')
        for line in lines:
            if line.strip():
                # Adiciona indentação para respostas longas
                if len(line) > 60 or ':' in line or '•' in line:
                    print(f"PANDORA: {line}")
                else:
                    print(f"PANDORA: {line}")
        
        # Adiciona sugestões se houver
        if response['suggested_actions']:
            print("\n📋 AÇÕES SUGERIDAS PELO PANDORA:")
            for action in response['suggested_actions']:
                print(f"  • {action}")

# ============================================================================
# DEMONSTRAÇÃO E TESTES
# ============================================================================

def demonstrate_PANDORA():
    """Demonstração das capacidades da PANDORA"""
    print("\n" + "="*80)
    print("DEMONSTRAÇÃO - PANDORA ESPECIALISTA EM SOBREVIVÊNCIA")
    print("="*80)
    
    PANDORA_instance = PANDORA()
    
    # Testa diferentes tipos de consultas
    test_queries = [
        "Como tratar um corte profundo que está sangrando muito?",
        "Estou perdido na floresta, o que fazer?",
        "Meu amigo queimou a mão no fogo, como tratar?",
        "Como fazer fogo sem fósforos?",
        "Quais plantas são seguras para comer?",
        "Como construir um abrigo rápido?"
    ]
    
    for i, query in enumerate(test_queries, 1):
        print(f"\n{'='*60}")
        print(f"TESTE {i}: {query}")
        print(f"{'='*60}")
        
        response = PANDORA_instance.process_query(query)
        
        # Mostra apenas parte da resposta para demonstração
        lines = response['content'].split('\n')
        for line in lines[:15]:  # Limita a 15 linhas por demonstração
            print(line)
        
        if len(lines) > 15:
            print("... (resposta truncada para demonstração)")
        
        input("\nPressione Enter para próxima consulta...")
    
    print(f"\n{'='*80}")
    print("FIM DA DEMONSTRAÇÃO DO PANDORA")
    print("="*80)

def test_environment_with_PANDORA():
    """Testa o ambiente com PANDORA integrada"""
    print("\n" + "="*80)
    print("TESTE DO AMBIENTE COM PANDORA")
    print("="*80)
    
    # Cria ambiente
    env = SurvivalEnvWithPANDORA(
        max_steps=50,
        difficulty=1.0,
        terrain="forest",
        enable_PANDORA=True,
        render_mode='human'
    )
    
    obs, info = env.reset()
    print(f"Estado inicial: Saúde={info['health']}, Inventário={info['inventory']}")
    
    # Testa algumas ações
    actions = [0, 1, 4, 12]  # Buscar água, caçar, descansar, consultar PANDORA
    
    for i, action in enumerate(actions):
        print(f"\n--- Ação {i+1} ---")
        
        if action >= 12:  # Ação PANDORA
            query = "primeiros socorros básicos"
            print(f"Consultando PANDORA: '{query}'")
            response = env.consult_PANDORA(query)
            print(f"Resposta (primeiras 3 linhas):")
            for line in response.split('\n')[:3]:
                print(f"  {line}")
        else:
            obs, reward, terminated, truncated, info = env.step(action)
            print(f"Ação: {action}, Recompensa: {reward:.2f}")
            print(f"Saúde: {info['health']:.1f}, Fome: {info['hunger']:.1f}")
            
            if 'action_info' in info and info['action_info']['message']:
                print(f"Resultado: {info['action_info']['message']}")
        
        env.render()
        
        if terminated or truncated:
            print("\nMissão terminada!")
            break
    
    # Obtém dica da PANDORA baseada no estado
    tip = env.get_PANDORA_tip()
    if tip:
        print(f"\n{'='*60}")
        print("DICA DA PANDORA (baseada no estado atual):")
        print(f"{'='*60}")
        for line in tip.split('\n')[:10]:
            print(line)
    
    env.close()
    
    return env

def interactive_PANDORA():
    """Inicia interface interativa com PANDORA"""
    print("\n" + "="*80)
    print("INICIANDO PANDORA - MODO INTERATIVO")
    print("="*80)
    print("\nCarregando conhecimentos de sobrevivência...")
    print("Carregando protocolos de primeiros socorros...")
    print("Sistema especialista PANDORA inicializado!")
    print("="*80)
    
    interface = PANDORAChatInterface()
    interface.start_conversation()

# ============================================================================
# EXECUÇÃO PRINCIPAL
# ============================================================================

if __name__ == "__main__":
    import argparse
    
    parser = argparse.ArgumentParser(description='PANDORA - Especialista em Sobrevivência e Primeiros Socorros')
    parser.add_argument('--mode', choices=['demo', 'test', 'chat', 'interactive'], default='chat',
                       help='Modo de execução: demo (demonstração), test (teste ambiente), chat (interface conversacional), interactive (completo)')
    
    args = parser.parse_args()
    
    if args.mode == 'demo':
        demonstrate_PANDORA()
    
    elif args.mode == 'test':
        test_environment_with_PANDORA()
    
    elif args.mode == 'chat':
        interactive_PANDORA()
    
    elif args.mode == 'interactive':
        # Execução completa
        print("\n" + "="*80)
        print("SISTEMA PANDORA - VERSÃO COMPLETA")
        print("="*80)
        
        # Demonstração
        demonstrate_PANDORA()
        
        # Teste do ambiente
        input("\nPressione Enter para testar ambiente com PANDORA...")
        test_environment_with_PANDORA()
        
        # Interface conversacional
        input("\nPressione Enter para iniciar interface conversacional...")
        interactive_PANDORA()
    
    print("\n" + "="*80)
    print("PANDORA - CONHECIMENTO QUE SALVA VIDAS")
    print("="*80)
