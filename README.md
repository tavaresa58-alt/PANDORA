    BURN_TREATMENT = {
 # ============================================================================
# PANDORA ENHANCED ULTIMATE - Sistema de Primeiros Socorros Offline
# Criado por: Alexander Chrysostomo Dias
# Versão: 2.0 (Completa e Robusta)
# Data: 2025
# ============================================================================
# 
# CARACTERÍSTICAS:
# - Totalmente offline (funciona sem internet)
# - Compatível com smartphones e computadores
# - Interface melhorada e intuitiva
# - Protocolos atualizados AHA 2025
# - Sistema de diagnóstico inteligente
# - Modo de treinamento prático
# - Banco de dados local de emergências
# - Sistema de salvamento automático
# ============================================================================

import json
import os
import sys
from datetime import datetime
from typing import Dict, List, Optional, Tuple
import hashlib

# ============================================================================
# CLASSE BASE PANDORA (compatibilidade)
# ============================================================================

class PANDORA:
    """
    Classe base original para compatibilidade
    """
    def __init__(self):
        self.protocols = {}
        self._load_base_protocols()
    
    def _load_base_protocols(self):
        """Carrega protocolos básicos"""
        self.protocols = {
            'heart_attack': "Dor no peito, falta de ar - chamar 192 imediatamente",
            'bleeding': "Aplicar pressão direta no ferimento",
            'burn': "Resfriar com água corrente por 20 minutos",
            'fracture': "Imobilizar sem tentar endireitar",
            'choking': "5 tapas nas costas + 5 compressões abdominais (Heimlich)"
        }
    
    def get_first_aid_instructions(self, emergency_type: str) -> str:
        """Retorna instruções de primeiros socorros"""
        return self.protocols.get(emergency_type.lower(), "Protocolo não encontrado")

# ============================================================================
# PANDORA ENHANCED ULTIMATE - Sistema Completo
# ============================================================================

class PANDORAEnhancedUltimate(PANDORA):
    """
    Versão Ultimate aprimorada da PANDORA
    Sistema completo de primeiros socorros offline
    """
    
    def __init__(self, data_dir: str = "./pandora_data"):
        super().__init__()
        
        # Configurações do sistema
        self.data_dir = data_dir
        self.user_preferences = {}
        self.emergency_history = []
        self.version = "2.0 Ultimate"
        self.creator = "Alexander Chrysostomo Dias"
        
        # Criar diretório de dados se não existir
        os.makedirs(data_dir, exist_ok=True)
        
        # Inicializar todos os módulos
        self._init_enhanced_protocols()
        self._init_diagnostic_system()
        self._init_training_system()
        self._init_quick_reference()
        self._init_offline_database()
        self._init_emergency_contacts()
        
        # Carregar histórico se existir
        self._load_user_data()
        
        # Sistema de verificação de integridade
        self._system_check()
    
    # ========================================================================
    # SISTEMA DE PROTOCOLOS AVANÇADOS
    # ========================================================================
    
    def _init_enhanced_protocols(self):
        """Carrega todos os protocolos atualizados 2025+"""
        
        # Protocolos principais baseados em AHA 2025, Red Cross, e protocolos militares
        self.PROTOCOLS_2025 = {
            # PARADA CARDÍACA - Atualizado AHA 2025
            'cardiac_arrest': {
                'name': 'Parada Cardíaca',
                'priority': 'CRÍTICA',
                'time_sensitive': True,
                'steps': [
                    ('1. Verificar segurança', 'Garantir que a cena é segura para aproximação'),
                    ('2. Verificar resposta', 'Chacoalhar suavemente os ombros e gritar "Você está bem?"'),
                    ('3. Chamar ajuda', 'Gritar por ajuda, pedir para alguém chamar 192/193 e trazer DEA'),
                    ('4. Verificar respiração', 'Observar se há respiração normal por no máximo 10 segundos'),
                    ('5. Iniciar RCP', '30 compressões torácicas (5-6cm profundidade, 100-120/min)'),
                    ('6. Ventilações', '2 ventilações (1 segundo cada) após cada 30 compressões'),
                    ('7. Usar DEA', 'Seguir instruções de voz do desfibrilador'),
                    ('8. Continuar', 'Manter RCP até: sinais de vida, DEA indicar, socorro chegar ou exaustão'),
                ],
                'notes': [
                    'Para leigos: compressões apenas também são eficazes',
                    'Evite interrupções nas compressões',
                    'Troque de socorrista a cada 2 minutos se possível',
                    'Verificar pulso carotídeo apenas se treinado'
                ],
                'source': 'AHA Guidelines 2025'
            },
            
            # INFARTO AGUDO DO MIOCÁRDIO
            'heart_attack': {
                'name': 'Infarto Cardíaco',
                'priority': 'CRÍTICA',
                'time_sensitive': True,
                'symptoms': [
                    'Dor ou desconforto no peito (pressão, aperto, queimação)',
                    'Dor que irradia para braço esquerdo, pescoço, mandíbula ou costas',
                    'Falta de ar ou dificuldade para respirar',
                    'Náusea, vômito ou tontura',
                    'Sudorese fria e palidez',
                    'Ansiedade extrema ou sensação de morte iminente'
                ],
                'steps': [
                    ('1. Sentar a vítima', 'Posição confortável, semi-sentada'),
                    ('2. Chamar 192', 'Informar suspeita de infarto imediatamente'),
                    ('3. Aspirina', 'Se disponível e não alérgico, mastigar 300mg de aspirina'),
                    ('4. Nitroglicerina', 'Se prescrita, administrar conforme orientação médica'),
                    ('5. Monitorar', 'Verificar consciência e respiração constantemente'),
                    ('6. Preparar RCP', 'Se perder consciência e parar de respirar, iniciar RCP')
                ],
                'source': 'SBC Guidelines 2024'
            },
            
            # HEMORRAGIA GRAVE
            'severe_bleeding': {
                'name': 'Hemorragia Grave',
                'priority': 'CRÍTICA',
                'time_sensitive': True,
                'steps': [
                    ('1. Proteção', 'Usar luvas se disponível ou criar barreira com plástico/pano'),
                    ('2. Expor ferida', 'Remover ou cortar roupa para visualizar completamente'),
                    ('3. Pressão direta', 'Aplicar pressão firme com pano limpo sobre o ferimento'),
                    ('4. Elevação', 'Elevar o membro acima do nível do coração se possível'),
                    ('5. Pressão arterial', 'Localizar artéria e comprimir contra osso se necessário'),
                    ('6. Torniquete', 'Considerar se: hemorragia arterial, múltiplas vítimas, ambiente hostil'),
                ],
                'torniquete_protocol': [
                    'Usar torniquete comercial ou improvisado (cinto, cadarço, tira de pano)',
                    'Aplicar 5-7cm acima do ferimento (NUNCA sobre articulação)',
                    'Apertar até parar o sangramento',
                    'Registrar horário da aplicação',
                    'NUNCA afrouxar ou remover - apenas profissional médico remove',
                    'Transportar vítima com torniquete visível'
                ],
                'source': 'Combat Life Saver - TCCC'
            },
            
            # TRAUMA CRÂNIO-ENCEFÁLICO
            'head_trauma': {
                'name': 'Trauma Craniano',
                'priority': 'ALTA',
                'symptoms': [
                    'Perda de consciência (mesmo que breve)',
                    'Confusão ou desorientação',
                    'Náusea ou vômito persistente',
                    'Pupilas desiguais ou que não reagem à luz',
                    'Sangramento ou saída de líquido claro pelo nariz/ouvido',
                    'Convulsões ou movimentos anormais',
                    'Fraqueza ou dormência em membros'
                ],
                'steps': [
                    ('1. Imobilização', 'Manter cabeça e coluna alinhadas manualmente'),
                    ('2. Não mover', 'Evitar movimentos desnecessários da cabeça/pescoço'),
                    ('3. Controle sangramento', 'Pressão leve ao redor (não sobre) ferida na cabeça'),
                    ('4. Monitorar consciência', 'Usar escala AVPU a cada 5 minutos'),
                    ('5. Posição lateral', 'Se vomitando e sem suspeita de coluna, colocar em posição lateral'),
                    ('6. Transporte urgente', 'Necessidade de avaliação por tomografia')
                ],
                'avpu_scale': {
                    'A': 'Alerta - responde normalmente',
                    'V': 'Voz - responde apenas à voz',
                    'P': 'Dor - responde apenas a estímulos dolorosos',
                    'U': 'Sem resposta - não responde a nenhum estímulo'
                },
                'source': 'ATLS Protocol 2024'
            },
            
            # AFOGAMENTO
            'drowning': {
                'name': 'Afogamento',
                'priority': 'CRÍTICA',
                'steps': [
                    ('1. Segurança do socorrista', 'Não entrar na água sem equipamento ou treinamento'),
                    ('2. Retirar da água', 'Usar objetos para alcançar ou lançar flutuador'),
                    ('3. Verificar respiração', 'Colocar em superfície rígida, abrir vias aéreas'),
                    ('4. Ventilações de resgate', 'Se não respira, dar 5 ventilações iniciais'),
                    ('5. RCP completa', '30 compressões : 2 ventilações (prioridade para oxigenação)'),
                    ('6. Aquecimento', 'Remover roupas molhadas, cobrir com cobertor seco'),
                    ('7. Posição lateral', 'Se recuperar consciência, colocar em posição de recuperação')
                ],
                'special_notes': [
                    'Hipotermia protege o cérebro - não desistir de reanimação precocemente',
                    'Vômito é comum - estar preparado para limpar vias aéreas',
                    'Todas as vítimas de afogamento necessitam avaliação hospitalar'
                ],
                'source': 'ILCOR 2025'
            },
            
            # QUEIMADURAS GRAVES
            'severe_burns': {
                'name': 'Queimaduras Graves',
                'classification': {
                    '1º grau': 'Vermelhidão apenas (como queimadura solar)',
                    '2º grau': 'Bolhas e dor intensa',
                    '3º grau': 'Pele branca ou carbonizada, pouca dor (terminações nervosas destruídas)'
                },
                'steps': [
                    ('1. Remover fonte', 'Parar o processo de queimadura (apagar chamas, remover químico)'),
                    ('2. Resfriar', 'Água corrente fria (não gelada) por 20 minutos'),
                    ('3. Remover objetos', 'Tirar anéis, relógios antes do inchaço'),
                    ('4. Cobrir', 'Pano limpo úmido ou gaze estéril (não algodão)'),
                    ('5. Não usar', 'NUNCA usar gelo, manteiga, pasta de dente ou óleo'),
                    ('6. Hidratar', 'Se consciente e sem náusea, oferecer água em pequenos goles'),
                    ('7. Choque', 'Monitorar sinais de choque (pulso rápido, palidez)')
                ],
                'hospital_criteria': [
                    'Queimaduras de 3º grau em qualquer tamanho',
                    'Queimaduras de 2º grau >10% da superfície corporal',
                    'Queimaduras em face, mãos, pés, genitais ou articulações',
                    'Queimaduras químicas ou elétricas',
                    'Vítimas com inalação de fumaça'
                ],
                'source': 'ABA Guidelines 2024'
            },
            
            # OVERDOSE DE OPIOIDES
            'opioid_overdose': {
                'name': 'Overdose de Opioides',
                'priority': 'CRÍTICA',
                'symptoms': [
                    'Respiração lenta ou ausente (<8 respirações/minuto)',
                    'Pupilas puntiformes (muito pequenas)',
                    'Pele pálida, fria e úmida',
                    'Lábios ou unhas azuladas',
                    'Roncos ou sons de sufocamento',
                    'Incapacidade de ser despertado'
                ],
                'steps': [
                    ('1. Chamar ajuda', 'Ligar 192 e informar suspeita de overdose'),
                    ('2. Verificar respiração', 'Observar, ouvir e sentir por 10 segundos'),
                    ('3. Ventilações', 'Se respirações <8/min, fornecer ventilações de resgate'),
                    ('4. Naloxona', 'Administrar spray nasal ou intramuscular se disponível'),
                    ('5. Posição lateral', 'Se respira sozinho, colocar em posição de recuperação'),
                    ('6. Monitorar', 'Efeito da naloxona dura 30-90 min - overdose pode retornar')
                ],
                'naloxone_administration': [
                    'Spray nasal: 1 spray em uma narina (4mg)',
                    'Intramuscular: Aplicar no músculo deltoide ou coxa',
                    'Repetir após 2-3 minutos se sem resposta',
                    'Pode ser necessário múltiplas doses em overdoses potentes'
                ],
                'source': 'SAMHSA Opioid Response 2024'
            }
        }
        
        # Protocolos adicionais
        self.ADDITIONAL_PROTOCOLS = {
            'diabetic_emergency': self._get_diabetic_protocol(),
            'stroke': self._get_stroke_protocol(),
            'allergic_reaction': self._get_allergy_protocol(),
            'hypothermia': self._get_hypothermia_protocol(),
            'heat_stroke': self._get_heat_stroke_protocol(),
            'seizure': self._get_seizure_protocol(),
            'childbirth': self._get_childbirth_protocol()
        }
    
    def _get_diabetic_protocol(self) -> dict:
        """Protocolo para emergências diabéticas"""
        return {
            'name': 'Emergência Diabética',
            'hypoglycemia': {
                'symptoms': ['Confusão', 'Sudorese', 'Tremores', 'Fome intensa', 'Agitação', 'Perda de consciência'],
                'treatment': [
                    'Se consciente: açúcar de ação rápida (suco, refrigerante, balas)',
                    'Repetir após 15 minutos se sem melhora',
                    'Se inconsciente: NÃO dar nada pela boca',
                    'Posição lateral de segurança',
                    'Chamar 192 se sem melhora em 15 minutos'
                ]
            },
            'hyperglycemia': {
                'symptoms': ['Sede extrema', 'Micção frequente', 'Hálito cetônico (frutado)', 'Náusea', 'Respiração profunda'],
                'treatment': [
                    'Monitorar nível de glicose se possível',
                    'Encaminhar para serviço médico',
                    'Se consciente: incentivar ingestão de água sem açúcar',
                    'Não administrar insulina sem prescrição médica'
                ]
            }
        }
    
    def _get_stroke_protocol(self) -> dict:
        """Protocolo para AVC usando escala FAST"""
        return {
            'name': 'Acidente Vascular Cerebral',
            'FAST': {
                'F': 'Face (rosto caído de um lado)',
                'A': 'Arm (braço fraco ou incapaz de levantar)',
                'S': 'Speech (fala arrastada ou estranha)',
                'T': 'Time (tempo crítico - chamar 192 IMEDIATAMENTE)'
            },
            'additional_signs': [
                'Perda súbita de visão em um ou ambos os olhos',
                'Dor de cabeça intensa e súbita',
                'Tontura, perda de equilíbrio ou coordenação',
                'Confusão ou dificuldade de compreensão'
            ],
            'actions': [
                'Chamar 192 imediatamente (cada minuto conta)',
                'Anotar hora do início dos sintomas',
                'Manter vítima confortável e calma',
                'Não dar comida, bebida ou medicamentos',
                'Se inconsciente, posição lateral de segurança',
                'Transporte para hospital com unidade de AVC'
            ],
            'time_window': {
                'trombólise': 'Até 4.5 horas do início dos sintomas',
                'trombectomia': 'Até 24 horas em casos selecionados'
            }
        }
    
    def _get_allergy_protocol(self) -> dict:
        """Protocolo para reação alérgica grave (anafilaxia)"""
        return {
            'name': 'Reação Alérgica Grave',
            'symptoms': [
                'Dificuldade para respirar ou sibilos',
                'Inchaço de língua, lábios ou garganta',
                'Erupção cutânea ou urticária generalizada',
                'Náusea, vômito ou diarreia',
                'Tontura ou desmaio',
                'Sensação de morte iminente'
            ],
            'steps': [
                'Administrar epinefrina (EpiPen) IMEDIATAMENTE se disponível',
                'Chamar 192 mesmo após administração de epinefrina',
                'Manher vítima deitada com pernas elevadas (exceto se dificuldade respiratória)',
                'Segunda dose de epinefrina pode ser necessária após 5-15 minutos',
                'Não dar anti-histamínicos como tratamento inicial para anafilaxia',
                'Transporte hospitalar obrigatório (reação bifásica possível)'
            ],
            'epinephrine_administration': [
                'Adultos: 0.3mg intramuscular (EpiPen ou similar)',
                'Crianças <25kg: 0.15mg intramuscular',
                'Aplicar na parte externa da coxa (pode ser através da roupa)',
                'Massagear área por 10 segundos após aplicação',
                'Efeitos colaterais comuns: taquicardia, tremor, ansiedade'
            ]
        }
    
    # ========================================================================
    # SISTEMA DE DIAGNÓSTICO INTELIGENTE
    # ========================================================================
    
    def _init_diagnostic_system(self):
        """Inicializa sistema de diagnóstico por sintomas"""
        
        self.DIAGNOSTIC_TREE = {
            'root': {
                'question': 'Qual o principal problema?',
                'options': {
                    '1': {'text': 'Problemas respiratórios', 'next': 'breathing'},
                    '2': {'text': 'Dor ou desconforto no peito', 'next': 'chest_pain'},
                    '3': {'text': 'Sangramento ou ferimento', 'next': 'bleeding'},
                    '4': {'text': 'Alteração de consciência', 'next': 'consciousness'},
                    '5': {'text': 'Trauma ou acidente', 'next': 'trauma'},
                    '6': {'text': 'Reação alérgica', 'next': 'allergy'},
                    '7': {'text': 'Queimadura', 'next': 'burns'},
                    '8': {'text': 'Intoxicação/envenenamento', 'next': 'poisoning'},
                    '9': {'text': 'Crise convulsiva', 'next': 'seizure'},
                    '10': {'text': 'Parto/emergência obstétrica', 'next': 'childbirth'}
                }
            },
            
            'chest_pain': {
                'question': 'A dor irradia para braço esquerdo, pescoço ou mandíbula?',
                'options': {
                    '1': {'text': 'Sim, irradia', 'protocol': 'heart_attack'},
                    '2': {'text': 'Não irradia', 'question': 'Há falta de ar ou dificuldade para respirar?'},
                    '3': {'text': 'Não sei', 'protocol': 'heart_attack'}  # Em dúvida, tratar como infarto
                }
            },
            
            'breathing': {
                'question': 'A pessoa está conseguindo falar frases completas?',
                'options': {
                    '1': {'text': 'Não, apenas palavras soltas', 'priority': 'ALTA'},
                    '2': {'text': 'Sim, mas com dificuldade', 'priority': 'MÉDIA'},
                    '3': {'text': 'Não consegue falar', 'protocol': 'choking', 'priority': 'CRÍTICA'}
                }
            },
            
            'consciousness': {
                'question': 'A pessoa responde quando você fala com ela?',
                'options': {
                    '1': {'text': 'Não responde a nada', 'protocol': 'cardiac_arrest', 'priority': 'CRÍTICA'},
                    '2': {'text': 'Responde apenas à dor', 'priority': 'ALTA'},
                    '3': {'text': 'Responde, mas confusa', 'question': 'Há sinais de AVC (face caída, braço fraco)?'}
                }
            },
            
            'bleeding': {
                'question': 'O sangramento é em jato pulsátil ou encharca um pano em segundos?',
                'options': {
                    '1': {'text': 'Sim, jato ou muito rápido', 'protocol': 'severe_bleeding', 'priority': 'CRÍTICA'},
                    '2': {'text': 'Não, é lento', 'question': 'O ferimento é grande ou profundo?'}
                }
            }
        }
        
        self.diagnostic_state = {
            'active': False,
            'current_node': 'root',
            'history': [],
            'suspected_protocol': None,
            'priority_level': None
        }
    
    def start_diagnostic(self, initial_symptom: str = None) -> str:
        """Inicia modo diagnóstico interativo"""
        self.diagnostic_state = {
            'active': True,
            'current_node': 'root',
            'history': [],
            'suspected_protocol': None,
            'priority_level': None
        }
        
        if initial_symptom:
            # Mapeia sintoma inicial para nó da árvore
            symptom_map = {
                'dor peito': 'chest_pain',
                'falta ar': 'breathing',
                'sangrando': 'bleeding',
                'desmaio': 'consciousness',
                'queimadura': 'burns',
                'convulsão': 'seizure'
            }
            
            for key, node in symptom_map.items():
                if key in initial_symptom.lower():
                    self.diagnostic_state['current_node'] = node
                    break
        
        return self._get_diagnostic_question()
    
    def _get_diagnostic_question(self) -> str:
        """Retorna a próxima pergunta do diagnóstico"""
        node = self.DIAGNOSTIC_TREE.get(self.diagnostic_state['current_node'])
        
        if not node:
            return "Diagnóstico não disponível para este sintoma."
        
        question = f"\n🔍 DIAGNÓSTICO: {node['question']}\n\n"
        
        for key, option in node['options'].items():
            question += f"{key}. {option['text']}\n"
        
        question += "\nDigite o número da opção ou 'sair' para cancelar: "
        
        return question
    
    def process_diagnostic_answer(self, answer: str) -> Tuple[str, bool]:
        """
        Processa resposta do usuário no diagnóstico
        Retorna: (resposta, diagnóstico_completo)
        """
        if not self.diagnostic_state['active']:
            return "Modo diagnóstico não iniciado.", True
        
        node = self.DIAGNOSTIC_TREE.get(self.diagnostic_state['current_node'])
        
        if answer.lower() in ['sair', 'exit', 'cancelar']:
            self.diagnostic_state['active'] = False
            return "Diagnóstico cancelado.", True
        
        option = node['options'].get(answer)
        
        if not option:
            return "Opção inválida. Por favor, digite o número da opção.", False
        
        # Registrar no histórico
        self.diagnostic_state['history'].append({
            'node': self.diagnostic_state['current_node'],
            'answer': answer,
            'text': option['text']
        })
        
        # Verificar se chegou a um protocolo
        if 'protocol' in option:
            self.diagnostic_state['suspected_protocol'] = option['protocol']
            self.diagnostic_state['priority_level'] = option.get('priority', 'MÉDIA')
            
            result = self._generate_diagnostic_report()
            self.diagnostic_state['active'] = False
            
            # Registrar no histórico de emergências
            self._log_emergency(
                protocol=option['protocol'],
                priority=option.get('priority', 'MÉDIA'),
                diagnostic_history=self.diagnostic_state['history']
            )
            
            return result, True
        
        # Ir para próxima pergunta
        elif 'next' in option:
            self.diagnostic_state['current_node'] = option['next']
            return self._get_diagnostic_question(), False
        
        # Próxima pergunta aninhada
        elif 'question' in option:
            follow_up = f"\n📋 {option['question']}\n\n"
            
            # Criar opções para pergunta de acompanhamento
            if 'chest_pain' in self.diagnostic_state['current_node']:
                follow_up += "1. Sim, há falta de ar\n2. Não, respira normal\n\nDigite 1 ou 2: "
                self.diagnostic_state['current_node'] = 'chest_pain_followup'
            elif 'bleeding' in self.diagnostic_state['current_node']:
                follow_up += "1. Sim, grande ou profundo\n2. Não, pequeno superficial\n\nDigite 1 ou 2: "
                self.diagnostic_state['current_node'] = 'bleeding_followup'
            
            return follow_up, False
        
        else:
            return "Não foi possível determinar o próximo passo.", True
    
    def _generate_diagnostic_report(self) -> str:
        """Gera relatório final do diagnóstico"""
        protocol = self.diagnostic_state['suspected_protocol']
        priority = self.diagnostic_state['priority_level']
        
        report = f"\n{'='*60}\n"
        report += "🚨 DIAGNÓSTICO CONCLUÍDO 🚨\n"
        report += f"{'='*60}\n\n"
        report += f"PROTOCOLO INDICADO: {protocol.upper()}\n"
        report += f"PRIORIDADE: {priority}\n\n"
        
        report += "📋 HISTÓRICO DE RESPOSTAS:\n"
        for i, entry in enumerate(self.diagnostic_state['history'], 1):
            report += f"{i}. {entry['text']}\n"
        
        report += f"\n📄 PROTOCOLO COMPLETO:\n"
        report += self.get_enhanced_protocol(protocol)
        
        report += f"\n⏱️ Hora do diagnóstico: {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}\n"
        
        return report
    
    # ========================================================================
    # SISTEMA DE TREINAMENTO INTERATIVO
    # ========================================================================
    
    def _init_training_system(self):
        """Inicializa sistema de treinamento com cenários"""
        
        self.TRAINING_SCENARIOS = {
            'cpr_basico': {
                'name': 'RCP Básico - Adulto',
                'description': 'Vítima adulta inconsciente sem respirar',
                'steps': [
                    '1. Verifique segurança da cena',
                    '2. Verifique responsividade (chacoalhe e grite)',
                    '3. Peça ajuda e peça para trazer DEA',
                    '4. Abra vias aéreas (inclinação da cabeça)',
                    '5. Verifique respiração (10 segundos)',
                    '6. Inicie 30 compressões torácicas',
                    '7. Faça 2 ventilações',
                    '8. Continue ciclo 30:2'
                ],
                'evaluation': {
                    'compressao_correta': '5-6cm de profundidade, 100-120/min, recoil completo',
                    'ventilacao_correta': '1 segundo cada, ver elevação do tórax',
                    'erros_comuns': [
                        'Compressões muito rasas',
                        'Interrupções prolongadas',
                        'Não permitir recoil completo',
                        'Hiperventilação'
                    ]
                }
            },
            
            'hemorragia': {
                'name': 'Controle de Hemorragia Grave',
                'description': 'Ferimento no braço com sangramento arterial',
                'steps': [
                    '1. Use luvas ou barreira de proteção',
                    '2. Exponha completamente o ferimento',
                    '3. Aplique pressão direta com pano limpo',
                    '4. Eleve o membro acima do coração',
                    '5. Se não parar, aplique pressão arterial',
                    '6. Considere torniquete se sangramento persistente'
                ],
                'torniquete_simulation': {
                    'material': 'Cinto, corda ou tira de pano (5cm largura mínima)',
                    'localizacao': '5-7cm acima do ferimento, não sobre articulação',
                    'apertar': 'Até parar sangramento distal',
                    'tempo': 'Anotar hora da aplicação',
                    'nao_remover': 'SOMENTE profissional médico remove'
                }
            },
            
            'avc': {
                'name': 'Reconhecimento de AVC',
                'description': 'Homem de 60 anos com fala arrastada',
                'test_fast': {
                    'F': 'Peça para sorrir - um lado do rosto está caído?',
                    'A': 'Peça para levantar ambos braços - um cai ou não sobe?',
                    'S': 'Peça para repetir uma frase - fala arrastada ou estranha?',
                    'T': 'Se qualquer sinal positivo - TEMPO DE CHAMAR 192!'
                },
                'actions': [
                    'Chamar 192 imediatamente',
                    'Anotar hora do início dos sintomas',
                    'Não dar comida, bebida ou medicamentos',
                    'Manter vítima calma e confortável',
                    'Transporte para hospital com unidade de AVC'
                ]
            }
        }
        
        self.training_scores = {}
    
    def start_training(self, scenario_name: str) -> str:
        """Inicia um cenário de treinamento"""
        scenario = self.TRAINING_SCENARIOS.get(scenario_name.lower())
        
        if not scenario:
            available = "\n".join([f"- {s}" for s in self.TRAINING_SCENARIOS.keys()])
            return f"Cenário não encontrado. Cenários disponíveis:\n{available}"
        
        training_text = f"\n🎯 CENÁRIO DE TREINAMENTO: {scenario['name']}\n"
        training_text += f"📝 Descrição: {scenario['description']}\n\n"
        training_text += "📋 PROCEDIMENTO RECOMENDADO:\n"
        
        for step in scenario.get('steps', []):
            training_text += f"{step}\n"
        
        if 'evaluation' in scenario:
            training_text += "\n📊 CRITÉRIOS DE AVALIAÇÃO:\n"
            for key, value in scenario['evaluation'].items():
                training_text += f"{key.replace('_', ' ').title()}: {value}\n"
        
        if 'test_fast' in scenario:
            training_text += "\n⚡ TESTE FAST PARA AVC:\n"
            for letter, instruction in scenario['test_fast'].items():
                training_text += f"{letter}: {instruction}\n"
        
        # Adicionar simulação interativa
        training_text += "\n🎮 MODO PRÁTICO:\n"
        training_text += "Para simular ações, digite:\n"
        training_text += "- 'compressor' para prática de compressões\n"
        training_text += "- 'torniquete' para simular aplicação de torniquete\n"
        training_text += "- 'avaliar' para testar reconhecimento de sinais\n"
        training_text += "- 'sair' para terminar treinamento\n"
        
        return training_text
    
    def simulate_action(self, scenario: str, action: str) -> str:
        """Simula uma ação específica no treinamento"""
        
        if action == 'compressor':
            return self._simulate_cpr()
        elif action == 'torniquete':
            return self._simulate_tourniquet()
        elif action == 'avaliar':
            return self._simulate_assessment(scenario)
        else:
            return f"Ação '{action}' não reconhecida. Use: compressor, torniquete, avaliar"
    
    def _simulate_cpr(self) -> str:
        """Simula prática de RCP"""
        import time
        
        simulation = "\n💓 SIMULAÇÃO DE RCP:\n"
        simulation += "Iniciando compressões...\n"
        simulation += "Meta: 100-120 compressões por minuto\n"
        simulation += "Profundidade: 5-6cm\n"
        simulation += "Recoil completo entre compressões\n\n"
        
        simulation += "Dica: cante 'Stayin' Alive' dos Bee Gees para manter ritmo\n"
        simulation += "Ou 'Another One Bites the Dust' (mesmo ritmo, mas menos apropriado!)\n"
        
        simulation += "\n⏱️ Pratique por 2 minutos (tempo até socorro chegar):\n"
        simulation += "Inicie contagem mental ou use cronômetro\n"
        simulation += "Após 30 compressões, simule 2 ventilações\n"
        
        return simulation
    
    def _simulate_tourniquet(self) -> str:
        """Simula aplicação de torniquete"""
        simulation = "\n🩹 SIMULAÇÃO DE TORNIQUETE:\n"
        simulation += "Material: use cinto, corda ou tira de pano (mínimo 5cm largura)\n\n"
        simulation += "PASSOS:\n"
        simulation += "1. Posicione 5-7cm acima do ferimento (NUNCA sobre articulação)\n"
        simulation += "2. Enrole firmemente ao redor do membro\n"
        simulation += "3. Use objeto rígido como tensor (caneta, bastão)\n"
        simulation += "4. Gire até parar sangramento distal\n"
        simulation += "5. Fixe tensor no lugar\n"
        simulation += "6. ANOTE HORÁRIO DA APLICAÇÃO\n"
        simulation += "7. NUNCA AFROUXE - só profissional médico remove\n\n"
        
        simulation += "⚠️ ATENÇÃO: Torniquete salva vidas em hemorragia arterial\n"
        simulation += "Dor intensa é normal - não afrouxe!\n"
        
        return simulation
    
    # ========================================================================
    # REFERÊNCIA RÁPIDA E UTILITÁRIOS
    # ========================================================================
    
    def _init_quick_reference(self):
        """Inicializa guias de referência rápida"""
        
        self.QUICK_GUIDES = {
            'numeros_emergencia': {
                '192': 'SAMU - Serviço de Atendimento Móvel de Urgência',
                '193': 'Bombeiros',
                '190': 'Polícia Militar',
                '188': 'CVV - Centro de Valorização da Vida',
                '199': 'Defesa Civil',
                '191': 'Polícia Rodoviária Federal'
            },
            
            'avpu': {
                'A': 'Alerta - Responde normalmente',
                'V': 'Voz - Responde apenas à voz',
                'P': 'Dor - Responde apenas à dor',
                'U': 'Sem resposta - Não responde a nada'
            },
            
            'regra_9': {
                'Cabeça': '9%',
                'Cada braço': '9% (total 18%)',
                'Tronco anterior': '18%',
                'Tronco posterior': '18%',
                'Cada perna': '18% (total 36%)',
                'Genitália': '1%'
            },
            
            'tempos_criticos': {
                'Parada cardíaca': 'RCP iniciada em <3 minutos',
                'AVC': 'Hospital em <3 horas para trombolítico',
                'Infarto': 'Cateterismo em <90 minutos',
                'Hemorragia': 'Choque irreversível em 30-60 minutos',
                'Afogamento': 'Reanimação em <10 minutos'
            },
            
            'kit_primeiros_socorros': [
                'Luvas descartáveis (pelo menos 3 pares)',
                'Gaze estéril (vários pacotes)',
                'Ataduras de diferentes tamanhos',
                'Esparadrapo hipoalergênico',
                'Tesoura de traumas (arredondada)',
                'Pinça',
                'Termômetro digital',
                'Máscara de RCP',
                'Soro fisiológico para limpeza',
                'Álcool em gel',
                'Analgésicos básicos (paracetamol, ibuprofeno)',
                'Antisséptico (povidona iodada ou clorexidina)',
                'Compressas frias instantâneas',
                'Manta térmica de emergência',
                'Lanterna com pilhas extras',
                'Lista de contatos de emergência'
            ]
        }
    
    def get_quick_reference(self, topic: str) -> str:
        """Retorna guia de referência rápida"""
        guide = self.QUICK_GUIDES.get(topic.lower())
        
        if not guide:
            available = "\n".join([f"- {t}" for t in self.QUICK_GUIDES.keys()])
            return f"Tópico não encontrado. Tópicos disponíveis:\n{available}"
        
        output = f"\n📋 REFERÊNCIA RÁPIDA: {topic.upper()}\n\n"
        
        if isinstance(guide, dict):
            for key, value in guide.items():
                output += f"{key}: {value}\n"
        elif isinstance(guide, list):
            for item in guide:
                output += f"• {item}\n"
        
        return output
    
    # ========================================================================
    # BANCO DE DADOS OFFLINE
    # ========================================================================
    
    def _init_offline_database(self):
        """Inicializa banco de dados local para informações médicas"""
        
        self.MEDICAL_DATABASE = {
            'medicamentos_comuns': {
                'aspirina': {
                    'uso_emergencia': 'Infarto cardíaco (mastigar 300mg)',
                    'contraindicacoes': 'Alergia, úlcera ativa, hemorragia ativa',
                    'dose': '300-325mg mastigar para infarto'
                },
                'paracetamol': {
                    'uso': 'Febre e dor',
                    'dose_maxima': '4g/dia (8 comprimidos de 500mg)',
                    'risco': 'Overdose causa lesão hepática grave'
                },
                'ibuprofeno': {
                    'uso': 'Inflamação, dor, febre',
                    'contraindicacoes': 'Asma, doença renal, úlcera',
                    'interacao': 'Evitar com outros AINEs'
                },
                'loratadina': {
                    'uso': 'Alergias leves',
                    'nao_eficaz': 'Para anafilaxia (usar epinefrina)'
                }
            },
            
            'doencas_cronicas': {
                'diabetes': {
                    'hipoglicemia': 'Açúcar <70mg/dL - tratar com açúcar rápido',
                    'hiperglicemia': 'Sede, micção frequente - buscar atendimento',
                    'kit_emergencia': 'Glicosímetro, açúcar rápido, glucagon'
                },
                'hipertensao': {
                    'crise_hipertensiva': 'PA >180/120 com sintomas - buscar atendimento',
                    'medicamentos': 'NÃO suspender abruptamente'
                },
                'asma': {
                    'ataque_grave': 'Inalar não alivia, fala frases curtas - URGENTE',
                    'medicamentos': 'Broncodilatador de alívio (azul) sempre à mão'
                },
                'epilepsia': {
                    'durante_convulsao': 'Proteger cabeça, NÃO colocar nada na boca',
                    'pos_convulsao': 'Posição lateral, recuperação pode levar minutos'
                }
            },
            
            'faixas_etarias': {
                'lactente': '0-1 ano',
                'crianca_pequena': '1-3 anos',
                'crianca': '4-12 anos',
                'adolescente': '13-18 anos',
                'adulto': '19-64 anos',
                'idoso': '65+ anos'
            },
            
            'vital_signs_normal': {
                'adulto': {
                    'frequencia_cardiaca': '60-100 bpm',
                    'frequencia_respiratoria': '12-20 rpm',
                    'pressao_arterial': '<120/80 mmHg',
                    'temperatura': '36.5-37.5°C'
                },
                'crianca': {
                    'frequencia_cardiaca': '70-120 bpm (varia com idade)',
                    'frequencia_respiratoria': '20-30 rpm',
                    'pressao_arterial': 'mais baixa que adulto'
                }
            }
        }
    
    def search_database(self, term: str) -> str:
        """Busca informação no banco de dados médico"""
        term_lower = term.lower()
        results = []
        
        # Buscar em todas as categorias
        for category, items in self.MEDICAL_DATABASE.items():
            if isinstance(items, dict):
                for key, value in items.items():
                    if term_lower in key.lower():
                        results.append(f"\n🔍 {category.upper()} - {key.upper()}:")
                        if isinstance(value, dict):
                            for k, v in value.items():
                                results.append(f"   {k}: {v}")
                        else:
                            results.append(f"   {value}")
        
        if results:
            return "\n".join(results)
        else:
            return f"Nenhum resultado encontrado para '{term}'"
    
    # ========================================================================
    # CONTATOS DE EMERGÊNCIA
    # ========================================================================
    
    def _init_emergency_contacts(self):
        """Inicializa sistema de contatos de emergência"""
        
        self.EMERGENCY_CONTACTS_FILE = os.path.join(self.data_dir, "contacts.json")
        self.contacts = []
        
        # Contatos padrão
        self.default_contacts = [
            {'name': 'SAMU', 'number': '192', 'type': 'emergency'},
            {'name': 'Bombeiros', 'number': '193', 'type': 'emergency'},
            {'name': 'Polícia', 'number': '190', 'type': 'emergency'},
            {'name': 'CVV', 'number': '188', 'type': 'support'}
        ]
        
        # Carregar contatos salvos
        self._load_contacts()
    
    def _load_contacts(self):
        """Carrega contatos do arquivo"""
        try:
            if os.path.exists(self.EMERGENCY_CONTACTS_FILE):
                with open(self.EMERGENCY_CONTACTS_FILE, 'r', encoding='utf-8') as f:
                    self.contacts = json.load(f)
            else:
                self.contacts = self.default_contacts.copy()
                self._save_contacts()
        except:
            self.contacts = self.default_contacts.copy()
    
    def _save_contacts(self):
        """Salva contatos no arquivo"""
        try:
            with open(self.EMERGENCY_CONTACTS_FILE, 'w', encoding='utf-8') as f:
                json.dump(self.contacts, f, ensure_ascii=False, indent=2)
        except:
            pass  # Falha silenciosa
    
    def add_contact(self, name: str, number: str, contact_type: str = 'personal'):
        """Adiciona novo contato de emergência"""
        self.contacts.append({
            'name': name,
            'number': number,
            'type': contact_type,
            'added': datetime.now().strftime('%d/%m/%Y')
        })
        self._save_contacts()
        return f"Contato '{name}' adicionado com sucesso."
    
    def get_contacts(self, contact_type: str = None) -> str:
        """Retorna lista de contatos"""
        output = "\n📞 CONTATOS DE EMERGÊNCIA:\n\n"
        
        filtered = self.contacts
        if contact_type:
            filtered = [c for c in self.contacts if c['type'] == contact_type]
        
        for contact in filtered:
            output += f"• {contact['name']}: {contact['number']}"
            if 'added' in contact:
                output += f" (desde {contact['added']})"
            output += "\n"
        
        return output
    
    # ========================================================================
    # FUNÇÕES PRINCIPAIS DA INTERFACE
    # ========================================================================
    
    def get_enhanced_protocol(self, emergency_type: str) -> str:
        """Retorna protocolo completo para emergência"""
        
        # Primeiro verificar nos protocolos principais
        if emergency_type.lower() in self.PROTOCOLS_2025:
            prot = self.PROTOCOLS_2025[emergency_type.lower()]
            return self._format_protocol(prot)
        
        # Verificar nos protocolos adicionais
        if emergency_type.lower() in self.ADDITIONAL_PROTOCOLS:
            prot = self.ADDITIONAL_PROTOCOLS[emergency_type.lower()]
            return self._format_protocol(prot)
        
        # Fallback para protocolo base
        base_instructions = super().get_first_aid_instructions(emergency_type)
        if base_instructions != "Protocolo não encontrado":
            return f"📋 PROTOCOLO BÁSICO: {emergency_type.upper()}\n\n{base_instructions}"
        
        # Se não encontrado em nenhum lugar
        available = "\n".join([f"- {p}" for p in list(self.PROTOCOLS_2025.keys()) + list(self.ADDITIONAL_PROTOCOLS.keys())])
        return f"Protocolo '{emergency_type}' não encontrado.\n\nProtocolos disponíveis:\n{available}"
    
    def _format_protocol(self, protocol: dict) -> str:
        """Formata protocolo para exibição"""
        output = f"\n{'='*60}\n"
        output += f"🚨 PROTOCOLO: {protocol.get('name', 'Desconhecido').upper()}\n"
        
        if 'priority' in protocol:
            output += f"⚠️ PRIORIDADE: {protocol['priority']}\n"
        
        output += f"{'='*60}\n\n"
        
        # Sintomas
        if 'symptoms' in protocol:
            output += "📋 SINAIS E SINTOMAS:\n"
            for symptom in protocol['symptoms']:
                output += f"• {symptom}\n"
            output += "\n"
        
        # Passos de ação
        if 'steps' in protocol:
            output += "📝 AÇÕES RECOMENDADAS:\n"
            for step in protocol['steps']:
                if isinstance(step, tuple):
                    output += f"{step[0]}: {step[1]}\n"
                else:
                    output += f"• {step}\n"
            output += "\n"
        
        # Informações específicas
        sections = ['torniquete_protocol', 'avpu_scale', 'special_notes', 
                   'hospital_criteria', 'naloxone_administration', 'time_window']
        
        for section in sections:
            if section in protocol:
                output += f"📌 {section.replace('_', ' ').upper()}:\n"
                if isinstance(protocol[section], dict):
                    for key, value in protocol[section].items():
                        output += f"  {key}: {value}\n"
                elif isinstance(protocol[section], list):
                    for item in protocol[section]:
                        output += f"• {item}\n"
                output += "\n"
        
        # Fonte
        if 'source' in protocol:
            output += f"📚 Fonte: {protocol['source']}\n"
        
        # Timestamp
        output += f"⏱️ Consultado em: {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}\n"
        
        return output
    
    def list_all_protocols(self) -> str:
        """Lista todos os protocolos disponíveis"""
        output = "\n📚 TODOS OS PROTOCOLOS DISPONÍVEIS:\n\n"
        
        output += "PRINCIPAIS PROTOCOLOS 2025:\n"
        for key, protocol in self.PROTOCOLS_2025.items():
            output += f"• {key}: {protocol.get('name', '')}\n"
        
        output += "\nPROTOCOLOS ADICIONAIS:\n"
        for key, protocol in self.ADDITIONAL_PROTOCOLS.items():
            output += f"• {key}: {protocol.get('name', '')}\n"
        
        output += f"\nTotal: {len(self.PROTOCOLS_2025) + len(self.ADDITIONAL_PROTOCOLS)} protocolos\n"
        
        return output
    
    # ========================================================================
    # SISTEMA DE HISTÓRICO E DADOS
    # ========================================================================
    
    def _load_user_data(self):
        """Carrega dados do usuário salvos"""
        history_file = os.path.join(self.data_dir, "history.json")
        prefs_file = os.path.join(self.data_dir, "preferences.json")
        
        try:
            if os.path.exists(history_file):
                with open(history_file, 'r', encoding='utf-8') as f:
                    self.emergency_history = json.load(f)
        except:
            self.emergency_history = []
        
        try:
            if os.path.exists(prefs_file):
                with open(prefs_file, 'r', encoding='utf-8') as f:
                    self.user_preferences = json.load(f)
        except:
            self.user_preferences = {}
    
    def _save_user_data(self):
        """Salva dados do usuário"""
        history_file = os.path.join(self.data_dir, "history.json")
        prefs_file = os.path.join(self.data_dir, "preferences.json")
        
        try:
            with open(history_file, 'w', encoding='utf-8') as f:
                json.dump(self.emergency_history[-50:], f, ensure_ascii=False, indent=2)  # Salva últimos 50
        except:
            pass
        
        try:
            with open(prefs_file, 'w', encoding='utf-8') as f:
                json.dump(self.user_preferences, f, ensure_ascii=False, indent=2)
        except:
            pass
    
    def _log_emergency(self, protocol: str, priority: str, diagnostic_history: list = None):
        """Registra uma consulta de emergência no histórico"""
        entry = {
            'timestamp': datetime.now().isoformat(),
            'protocol': protocol,
            'priority': priority,
            'history': diagnostic_history or []
        }
        
        self.emergency_history.append(entry)
        
        # Manter histórico limitado
        if len(self.emergency_history) > 100:
            self.emergency_history = self.emergency_history[-100:]
        
        self._save_user_data()
    
    def get_history(self, limit: int = 10) -> str:
        """Retorna histórico de consultas"""
        if not self.emergency_history:
            return "Nenhum histórico registrado."
        
        output = "\n📊 HISTÓRICO DE CONSULTAS:\n\n"
        
        for i, entry in enumerate(self.emergency_history[-limit:], 1):
            dt = datetime.fromisoformat(entry['timestamp'])
            output += f"{i}. {dt.strftime('%d/%m/%Y %H:%M')} - {entry['protocol'].upper()} ({entry['priority']})\n"
        
        return output
    
    # ========================================================================
    # VERIFICAÇÃO DO SISTEMA
    # ========================================================================
    
    def _system_check(self) -> bool:
        """Verifica integridade do sistema"""
        try:
            # Verificar protocolos carregados
            assert len(self.PROTOCOLS_2025) > 0, "Protocolos não carregados"
            assert len(self.ADDITIONAL_PROTOCOLS) > 0, "Protocolos adicionais não carregados"
            
            # Verificar diretório de dados
            assert os.path.exists(self.data_dir), f"Diretório de dados não existe: {self.data_dir}"
            
            # Verificar funcionalidades básicas
            test_protocol = self.get_enhanced_protocol('cardiac_arrest')
            assert 'RCP' in test_protocol, "Protocolo de RCP não funcionando"
            
            print(f"✅ Sistema PANDORA Enhanced {self.version} carregado com sucesso!")
            print(f"👤 Criado por: {self.creator}")
            print(f"📁 Dados salvos em: {os.path.abspath(self.data_dir)}")
            print(f"📚 Protocolos carregados: {len(self.PROTOCOLS_2025) + len(self.ADDITIONAL_PROTOCOLS)}")
            
            return True
            
        except Exception as e:
            print(f"⚠️ Aviso no carregamento: {e}")
            print("Sistema carregado em modo de segurança.")
            return False
    
    def get_system_info(self) -> str:
        """Retorna informações do sistema"""
        info = f"\n{'='*60}\n"
        info += f"PANDORA ENHANCED ULTIMATE v{self.version}\n"
        info += f"Criado por: {self.creator}\n"
        info += f"{'='*60}\n\n"
        
        info += "📊 ESTATÍSTICAS DO SISTEMA:\n"
        info += f"• Protocolos carregados: {len(self.PROTOCOLS_2025) + len(self.ADDITIONAL_PROTOCOLS)}\n"
        info += f"• Consultas no histórico: {len(self.emergency_history)}\n"
        info += f"• Contatos salvos: {len(self.contacts)}\n"
        info += f"• Diretório de dados: {os.path.abspath(self.data_dir)}\n"
        
        info += "\n🔧 FUNCIONALIDADES:\n"
        info += "• Diagnóstico interativo por sintomas\n"
        info += "• Protocolos atualizados AHA 2025\n"
        info += "• Treinamento prático com simulação\n"
        info += "• Banco de dados médico offline\n"
        info += "• Contatos de emergência personalizáveis\n"
        info += "• Histórico de consultas automático\n"
        info += "• Referência rápida de primeiros socorros\n"
        
        info += "\n📱 COMPATIBILIDADE:\n"
        info += "• Funciona totalmente OFFLINE\n"
        info += "• Compatível com smartphones e computadores\n"
        info += "• Leve e rápido\n"
        
        return info


# ============================================================================
# INTERFACE DE CHAT MELHORADA
# ============================================================================

class PANDORAChatInterfaceEnhanced:
    """
    Interface de chat aprimorada para o sistema PANDORA Enhanced
    """
    
    def __init__(self, data_dir: str = "./pandora_data"):
        self.pandora = PANDORAEnhancedUltimate(data_dir)
        self.running = True
        self.in_diagnostic = False
        self.in_training = False
        
        # Comandos disponíveis
        self.COMMANDS = {
            'protocolo': 'Buscar protocolo específico (ex: "protocolo cardiac_arrest")',
            'diagnostico': 'Iniciar diagnóstico por sintomas',
            'treinar': 'Iniciar treinamento prático',
            'lista': 'Listar todos os protocolos disponíveis',
            'contatos': 'Ver/gerenciar contatos de emergência',
            'historico': 'Ver histórico de consultas',
            'buscar': 'Buscar informação no banco de dados',
            'referencia': 'Guia de referência rápida',
            'sistema': 'Informações do sistema',
            'ajuda': 'Mostrar esta ajuda',
            'sair': 'Encerrar o programa'
        }
    
    def display_welcome(self):
        """Exibe mensagem de boas-vindas"""
        welcome = f"""
{'='*70}
PANDORA ENHANCED ULTIMATE v{self.pandora.version}
SISTEMA COMPLETO DE PRIMEIROS SOCORROS OFFLINE
Criado por: {self.pandora.creator}
{'='*70}

🚑 Este sistema fornece instruções de primeiros socorros baseadas em 
protocolos internacionais atualizados (AHA 2025, Red Cross, TCCC).

⚠️ IMPORTANTE:
• Este sistema NÃO substitui atendimento médico profissional
• Em emergências reais, CHAME 192 IMEDIATAMENTE
• Use apenas como guia até a chegada do socorro qualificado

📱 Sistema totalmente offline - funciona sem internet em qualquer dispositivo
"""
        print(welcome)
        print("Digite 'ajuda' para ver todos os comandos disponíveis\n")
    
    def display_help(self):
        """Exibe ajuda dos comandos"""
        print("\n📋 COMANDOS DISPONÍVEIS:\n")
        for cmd, desc in self.COMMANDS.items():
            print(f"  {cmd:15} - {desc}")
        
        print("\n📝 EXEMPLOS DE USO:")
        print("  • protocolo cardiac_arrest")
        print("  • diagnostico dor no peito")
        print("  • treinar cpr_basico")
        print("  • buscar diabetes")
        print("  • referencia numeros_emergencia")
        print("  • contatos")
        print()
    
    def process_command(self, user_input: str) -> str:
        """Processa comando do usuário"""
        if not user_input.strip():
            return ""
        
        parts = user_input.lower().split()
        command = parts[0]
        
        # Comandos especiais em modo diagnóstico
        if self.in_diagnostic and command not in ['sair', 'exit', 'cancelar']:
            result, complete = self.pandora.process_diagnostic_answer(command)
            if complete:
                self.in_diagnostic = False
            return result
        
        # Comandos especiais em modo treino
        if self.in_training and command in ['compressor', 'torniquete', 'avaliar', 'sair']:
            if command == 'sair':
                self.in_training = False
                return "Treinamento encerrado."
            return self.pandora.simulate_action(self.current_training_scenario, command)
        
        # Comandos normais
        if command == 'sair' or command == 'exit':
            self.running = False
            return "Encerrando PANDORA Enhanced. Até logo!"
        
        elif command == 'ajuda':
            self.display_help()
            return ""
        
        elif command == 'protocolo':
            if len(parts) < 2:
                return "Digite: protocolo [nome_do_protocolo]\nEx: protocolo cardiac_arrest"
            protocol_name = " ".join(parts[1:])
            return self.pandora.get_enhanced_protocol(protocol_name)
        
        elif command == 'diagnostico' or command == 'diagnóstico':
            symptom = " ".join(parts[1:]) if len(parts) > 1 else None
            self.in_diagnostic = True
            return self.pandora.start_diagnostic(symptom)
        
        elif command == 'treinar' or command == 'treino':
            if len(parts) < 2:
                scenarios = "\n".join([f"  • {s}" for s in self.pandora.TRAINING_SCENARIOS.keys()])
                return f"Digite: treinar [cenario]\nCenários disponíveis:\n{scenarios}"
            
            scenario_name = parts[1]
            self.in_training = True
            self.current_training_scenario = scenario_name
            return self.pandora.start_training(scenario_name)
        
        elif command == 'lista':
            return self.pandora.list_all_protocols()
        
        elif command == 'contatos':
            if len(parts) > 1 and parts[1] == 'adicionar':
                if len(parts) < 4:
                    return "Uso: contatos adicionar [nome] [numero]"
                name = parts[2]
                number = parts[3]
                return self.pandora.add_contact(name, number)
            else:
                return self.pandora.get_contacts()
        
        elif command == 'historico' or command == 'histórico':
            limit = int(parts[1]) if len(parts) > 1 and parts[1].isdigit() else 10
            return self.pandora.get_history(limit)
        
        elif command == 'buscar':
            if len(parts) < 2:
                return "Digite: buscar [termo]\nEx: buscar diabetes, buscar aspirina"
            term = " ".join(parts[1:])
            return self.pandora.search_database(term)
        
        elif command == 'referencia' or command == 'referência':
            if len(parts) < 2:
                topics = "\n".join([f"  • {t}" for t in self.pandora.QUICK_GUIDES.keys()])
                return f"Digite: referencia [topico]\nTópicos disponíveis:\n{topics}"
            topic = parts[1]
            return self.pandora.get_quick_reference(topic)
        
        elif command == 'sistema':
            return self.pandora.get_system_info()
        
        else:
            # Tentar como protocolo direto
            result = self.pandora.get_enhanced_protocol(user_input)
            if "não encontrado" not in result.lower():
                return result
            
            return f"Comando não reconhecido: '{user_input}'\nDigite 'ajuda' para ver os comandos disponíveis."
    
    def start_conversation(self):
        """Inicia a interface de conversação"""
        self.display_welcome()
        
        while self.running:
            try:
                # Exibir prompt apropriado
                if self.in_diagnostic:
                    prompt = "🔍 Diagnóstico > "
                elif self.in_training:
                    prompt = "🎯 Treinamento > "
                else:
                    prompt = "🚑 PANDORA > "
                
                # Obter entrada do usuário
                user_input = input(prompt).strip()
                
                # Processar comando
                response = self.process_command(user_input)
                
                # Exibir resposta
                if response:
                    print(response)
                    
                    # Linha separadora
                    if not self.in_diagnostic and not self.in_training:
                        print("\n" + "-"*50 + "\n")
                
            except KeyboardInterrupt:
                print("\n\nInterrompido pelo usuário.")
                self.running = False
            except Exception as e:
                print(f"\n⚠️ Erro: {e}")
                print("Digite 'ajuda' para ver os comandos ou 'sair' para encerrar.")


# ============================================================================
# PONTO DE ENTRADA PRINCIPAL
# ============================================================================

def main():
    """
    Função principal para executar o sistema PANDORA Enhanced Ultimate
    """
    print("="*70)
    print("INICIANDO PANDORA ENHANCED ULTIMATE")
    print("Sistema de Primeiros Socorros Offline Completo")
    print("="*70)
    
    # Verificar se é primeira execução
    data_dir = "./pandora_data"
    first_run = not os.path.exists(data_dir)
    
    # Criar interface
    interface = PANDORAChatInterfaceEnhanced(data_dir)
    
    # Mensagem de primeira execução
    if first_run:
        print("\n🎉 PRIMEIRA EXECUÇÃO DETECTADA!")
        print("📁 Criando diretório de dados...")
        print("📚 Carregando banco de protocolos...")
        print("✅ Sistema pronto para uso offline!\n")
    
    # Iniciar interface
    interface.start_conversation()
    
    # Mensagem de encerramento
    print("\n" + "="*70)
    print("OBRIGADO POR USAR PANDORA ENHANCED ULTIMATE")
    print("Criado por: Alexander Chrysostomo Dias")
    print("Lembre-se: em emergências reais, CHAME 192!")
    print("="*70)


# ============================================================================
# EXECUÇÃO DIRETA
# ============================================================================

if __name__ == "__main__":
    # Verificar se está em ambiente mobile (Android/iOS via Pythonista/etc)
    try:
        import android
        IS_MOBILE = True
        print("📱 Modo mobile detectado")
    except:
        IS_MOBILE = False
    
    # Executar sistema
    main()       'FIRST_DEGREE': {
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
