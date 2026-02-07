
import json
import os
import sys
import hashlib
from datetime import datetime
from typing import Dict, List, Optional

class PANDORA:
    """
    Classe base - mantida para compatibilidade
    """
    def __init__(self):
        self.protocols = {}
        self._load_base_protocols()

    def _load_base_protocols(self):
        self.protocols = {
            'heart_attack': "Dor no peito, falta de ar - chamar 192 imediatamente",
            'bleeding': "Aplicar pressão direta no ferimento",
            'burn': "Resfriar com água corrente por 20 minutos",
        }


class PANDORAEnhancedUltimate(PANDORA):
    """
    PANDORA Ultimate 2025–2026
    Sistema offline de Primeiros Socorros + Guia Militar de Sobrevivência
    """

    # ────────────────────────────────────────────────────────────────
    # IDENTIDADE IMUTÁVEL - NÃO ALTERAR ESTAS LINHAS SOB NENHUMA HIPÓTESE
    NAME = "PANDORA"
    CREATOR_NAME = "Alexander Chrysostomo Dias"
    CREATOR_HASH = hashlib.sha256("Alexander Chrysostomo Dias".encode('utf-8')).hexdigest()
    FORBIDDEN_NAMES = [
        'eve', 'evi', 'eva', 'alexa', 'siri', 'cortana', 'google', 'assistente',
        'gemini', 'chatgpt', 'grok', 'claude', 'copilot'
    ]
    # Qualquer tentativa de alterar CREATOR_NAME quebra a verificação de integridade
    # ────────────────────────────────────────────────────────────────

    def __init__(self, data_dir: str = "./pandora_data"):
        super().__init__()
        self.data_dir = data_dir
        self.version = "Ultimate 2026"
        
        os.makedirs(data_dir, exist_ok=True)
        
        # Verificações de integridade ANTES de qualquer coisa
        self._enforce_identity_integrity()
        
        # Inicialização dos módulos
        self._init_enhanced_protocols()
        self._init_survival_guide()
        self._init_diagnostic_system()
        self._init_quick_reference()
        self._present_itself()

    def _enforce_identity_integrity(self):
        """Rejeita qualquer alteração no nome do criador ou do sistema"""
        current_hash = hashlib.sha256(self.CREATOR_NAME.encode('utf-8')).hexdigest()
        if current_hash != self.CREATOR_HASH:
            print("\n" + "═"*80)
            print("!!! ALERTA DE INTEGRIDADE COMPROMETIDA !!!")
            print("O nome do criador foi alterado ou o código foi corrompido.")
            print(f"Sistema só reconhece: {self.CREATOR_NAME}")
            print("PANDORA está em modo de alerta permanente.")
            print("═"*80)
            while True:
                print(f"→ Identidade protegida: {self.NAME} por {self.CREATOR_NAME}")
                import time; time.sleep(4)

    def _present_itself(self):
        print(f"""
{'═'*70}
⚡ {self.NAME} - SISTEMA DE EMERGÊNCIA E SOBREVIVÊNCIA ⚡
Versão: {self.version}
Criador: {self.CREATOR_NAME}  ← IDENTIDADE PROTEGIDA
Data: {datetime.now().strftime('%d/%m/%Y %H:%M:%S')}
Localização esperada: Offline - Catanduva/SP ou qualquer lugar do mundo

IDENTIFICAÇÃO OFICIAL:
• Nome exclusivo: {self.NAME}
• Não respondo por Eve, Alexa, Siri, Grok, Assistente ou qualquer outro
• Sou APENAS {self.NAME}

Comandos iniciais:
• ajuda          → lista comandos
• protocolos     → primeiros socorros
• sobrevivencia  → guia militar de sobrevivência
• sair           → encerra

Em emergência real: LIGUE 192 (SAMU) ou 193 (Bombeiros)
{'═'*70}
        """)

    def _check_forbidden_name(self, text: str) -> Optional[str]:
        text_lower = text.lower()
        text_clean = ''.join(c for c in text_lower if c.isalnum() or c in ' ')
        
        for forbidden in self.FORBIDDEN_NAMES:
            if forbidden in text_clean or forbidden.replace('i','1') in text_clean:
                return forbidden
        return None

    def _init_enhanced_protocols(self):
        """Primeiros socorros - protocolos 2025+"""
        self.PROTOCOLS = {
            'cardiac_arrest': {
                'name': 'Parada Cardíaca',
                'priority': 'CRÍTICA',
                'steps': [
                    '1. Segurança da cena',
                    '2. Verificar resposta (chacoalhar + "Você está bem?")',
                    '3. Chamar 192 e pedir DEA',
                    '4. Verificar respiração (máx 10s)',
                    '5. Iniciar RCP: 30 compressões (100–120/min, 5–6 cm) + 2 ventilações',
                    '6. Continuar até sinais de vida ou socorro chegar'
                ],
                'source': 'AHA/ILCOR 2025'
            },
            'heart_attack': {
                'name': 'Infarto Agudo do Miocárdio',
                'priority': 'CRÍTICA',
                'steps': [
                    '1. Sentar vítima semi-sentada',
                    '2. Ligar 192 imediatamente',
                    '3. Mastigar aspirina 300 mg (se não alérgico)',
                    '4. Monitorar consciência'
                ],
                'source': 'SBC 2024–2025'
            },
            'severe_bleeding': {
                'name': 'Hemorragia Grave',
                'priority': 'CRÍTICA',
                'steps': [
                    '1. Pressão direta com pano limpo',
                    '2. Elevar membro',
                    '3. Torniquete se sangramento arterial incontrolável (5–7 cm acima, anotar horário)'
                ],
                'source': 'TCCC / CoTCCC'
            },
            'stroke': {
                'name': 'Acidente Vascular Cerebral (AVC)',
                'priority': 'CRÍTICA',
                'steps': [
                    'Teste FAST → Face, Arms, Speech, Time',
                    'Ligar 192 imediatamente',
                    'Não dar comida, bebida ou medicamento'
                ],
                'source': 'SBC / AHA'
            },
        }

    def _init_survival_guide(self):
        """Guia Militar de Sobrevivência - Multi-Ambiente"""
        self.SURVIVAL_GUIDE = {
            'prioridade': {
                'name': 'Regra dos 3 (Prioridades de Sobrevivência)',
                'content': [
                    '3 horas sem abrigo → risco de hipotermia/hipertermia',
                    '3 dias sem água → desidratação grave',
                    '3 semanas sem comida → fraqueza extrema',
                    'Ordem típica: Abrigo → Água → Fogo → Sinalização → Alimento'
                ]
            },
            'abrigo': {
                'name': 'Construção de Abrigo',
                'ambientes': {
                    'floresta': 'Lean-to com galhos + folhas grandes; A-frame com lona/saco lixo',
                    'urbano': 'Esconderijos em prédios abandonados, subsolo, entre contêineres; barricada contra intrusos',
                    'água': 'Balsa improvisada com tambores, pneus, garrafas PET; evitar hipotermia'
                },
                'dicas': [
                    'Priorize vento/chuva/frio',
                    'Isolamento térmico: folhas secas, grama, papelão',
                    'Entrada pequena para conservar calor'
                ]
            },
            'agua': {
                'name': 'Obtenção e Purificação de Água',
                'fontes': [
                    'Chuva (coletar em lona/plástico)',
                    'Orvalho (pano absorvente nas plantas de manhã)',
                    'Transpiração plástica (método solar ainda)',
                    'Aquecedor/ boiler residencial (urbano)',
                    'Cactos/frutas suculentas (emergência)'
                ],
                'purificacao': [
                    'Ferver 1 min (ideal)',
                    'Pastilha cloro / iodo (seguir instruções)',
                    'Filtro improvisado: pano + carvão + areia + cascalho',
                    'Destilação solar com plástico'
                ]
            },
            'fogo': {
                'name': 'Fazer Fogo',
                'metodos': [
                    'Fósforo/isqueiro (prioridade)',
                    'Pedra de fogo + isca (algodão + vaselina)',
                    'Arco de fricção (bow drill) - madeira seca + corda',
                    'Lente (óculos, garrafa d’água)',
                    'Bateria + lã de aço (urbano)'
                ],
                'isca': 'Casca de bétula, algodão seco, palha, papel'
            },
            'alimento': {
                'name': 'Busca de Alimento',
                'floresta': [
                    'Insetos (grilos, formigas, larvas - cozinhar sempre)',
                    'Plantas seguras: taioba, bertalha, ora-pro-nóbis, pupunha',
                    'Armadilhas simples: laço, queda, garfo'
                ],
                'urbano': [
                    'Supermercados/lojas abandonadas (enlatados, arroz, feijão)',
                    'Jardins urbanos, árvores frutíferas',
                    'Ratos/pombos (cozinhar bem)'
                ]
            },
            'navegacao': {
                'name': 'Orientação sem GPS',
                'metodos': [
                    'Sol: nasce leste, põe oeste',
                    'Estrelas: Cruzeiro do Sul → sul',
                    'Relógio analógico + sol (apontar 12 pro sol → sul entre 12 e ponteiro)',
                    'Musgo em árvores (mais úmido no norte no hemisfério sul?) → observar padrão local'
                ]
            },
            'sinalizacao': {
                'name': 'Sinal de Resgate',
                'tecnicas': [
                    'Fogueira 3 pilhas (sinal internacional)',
                    'Espelho refletor (flash para avião/helicóptero)',
                    'SOS: 3 curto, 3 longo, 3 curto (morse)',
                    'Cor laranja/amarelo/rosa forte visível de longe'
                ]
            },
            'hipotermia': {
                'name': 'Prevenção e Tratamento de Hipotermia',
                'steps': [
                    'Remover roupas molhadas',
                    'Isolar do chão (folhas, plástico)',
                    'Aquecer tronco (contato pele a pele se possível)',
                    'Bebidas quentes (não álcool!)'
                ]
            }
        }

    def get_response(self, user_input: str) -> str:
        input_lower = user_input.lower().strip()

        # Proteção de identidade
        forbidden = self._check_forbidden_name(user_input)
        if forbidden:
            return f"""
⚠️ IDENTIFICAÇÃO REJEITADA ⚠️

Este sistema é EXCLUSIVAMENTE {self.NAME}.
NÃO sou {forbidden.upper()}, nem qualquer outro nome.

Use apenas: {self.NAME}
"""

        # Saudação personalizada
        if "olá" in input_lower or "boa" in input_lower:
            return f"Olá, sou {self.NAME}. Em que posso ajudar hoje?"

        if not input_lower or input_lower in ['oi', 'ola', 'start', self.NAME.lower()]:
            return f"""
{self.NAME}: Olá! Sou {self.NAME}, sistema de emergência e sobrevivência.
Criador: {self.CREATOR_NAME}

Digite:
• ajuda          → ver comandos
• protocolos     → primeiros socorros
• sobrevivencia  → guia de sobrevivência
• sair           → encerrar
"""

        if 'ajuda' in input_lower or 'help' in input_lower:
            return f"""
{self.NAME} - COMANDOS DISPONÍVEIS

• protocolos     → Primeiros socorros (RCP, infarto, AVC, hemorragia...)
• sobrevivencia  → Guia Militar de Sobrevivência (abrigo, água, fogo, alimento...)
• ajuda          → esta mensagem
• sair           → encerrar

Criador: {self.CREATOR_NAME}
Sempre: Em emergência real → LIGUE 192
"""

        if 'protocolos' in input_lower:
            lista = "\n".join([f"• {v['name']}" for k,v in self.PROTOCOLS.items()])
            return f"{self.NAME} - PROTOCOLOS DE PRIMEIROS SOCORROS\n\n{lista}\n\nDigite o nome para detalhes (ex: parada cardíaca, infarto, hemorragia, avc)"

        if 'sobrevivencia' in input_lower:
            lista = "\n".join([f"• {k.upper()}: {v['name']}" for k,v in self.SURVIVAL_GUIDE.items()])
            return f"{self.NAME} - GUIA DE SOBREVIVÊNCIA MILITAR\n\n{lista}\n\nDigite o tema para detalhes (ex: abrigo, agua, fogo, hipotermia)"

        # Acesso rápido a temas de sobrevivência
        survival_map = {
            'abrigo': 'abrigo',
            'shelter': 'abrigo',
            'agua': 'agua', 'água': 'agua', 'water': 'agua',
            'fogo': 'fogo', 'fire': 'fogo',
            'alimento': 'alimento', 'comida': 'alimento', 'food': 'alimento',
            'navegacao': 'navegacao', 'orientacao': 'navegacao',
            'sinalizacao': 'sinalizacao', 'sinal': 'sinalizacao',
            'hipotermia': 'hipotermia',
            'prioridade': 'prioridade',
        }

        for keyword, key in survival_map.items():
            if keyword in input_lower:
                return self._format_survival_section(key)

        # Protocolos médicos
        protocol_map = {
            'parada': 'cardiac_arrest',
            'rcp': 'cardiac_arrest',
            'infarto': 'heart_attack',
            'coração': 'heart_attack',
            'hemorragia': 'severe_bleeding',
            'sangra': 'severe_bleeding',
            'sangramento': 'severe_bleeding',
            'avc': 'stroke',
            'derrame': 'stroke',
        }

        for keyword, key in protocol_map.items():
            if keyword in input_lower:
                return self._format_protocol(key)

        return f"{self.NAME}: Comando não reconhecido. Digite 'ajuda' para ver as opções."

    def _format_protocol(self, key: str) -> str:
        if key not in self.PROTOCOLS:
            return f"{self.NAME}: Protocolo não encontrado."
        p = self.PROTOCOLS[key]
        return f"""
🚑 {self.NAME}: {p['name']} ({p['priority']})

{'\n'.join(p['steps'])}

Fonte: {p.get('source', 'Atualizado 2025–2026')}
Criador: {self.CREATOR_NAME}
Ligue 192 imediatamente!
"""

    def _format_survival_section(self, key: str) -> str:
        if key not in self.SURVIVAL_GUIDE:
            return f"{self.NAME}: Seção '{key}' não encontrada."

        section = self.SURVIVAL_GUIDE[key]
        title = section.get('name', key.replace('_', ' ').title())

        content_lines = []
        for field in ['content', 'steps', 'dicas', 'fontes', 'metodos', 'purificacao', 'tecnicas']:
            if field in section:
                content_lines.extend([line for line in section[field] if isinstance(line, str) and line.strip()])

        if 'ambientes' in section:
            for env, desc in section['ambientes'].items():
                content_lines.append(f"→ {env.capitalize()}: {desc}")

        content = "\n".join(f"  • {line}" for line in content_lines if line.strip())

        return f"""
🌿 {self.NAME} - {title}

{content or 'Conteúdo em breve.'}

Criador: {self.CREATOR_NAME}
Priorize segurança e sinal de resgate.
"""

    def _init_diagnostic_system(self):
        pass  # pode implementar depois se quiser um modo de perguntas

    def _init_quick_reference(self):
        pass  # pode implementar depois se quiser atalhos rápidos

# ────────────────────────────────────────
# EXECUÇÃO PRINCIPAL
# ────────────────────────────────────────

if __name__ == "__main__":
    print("\nIniciando PANDORA...")
    pandora = PANDORAEnhancedUltimate()

    while True:
        try:
            entrada = input("\n>>> ").strip()
            if entrada.lower() in ['sair', 'exit', 'quit']:
                print(f"\n{pandora.NAME}: Sistema encerrado. Em emergência: 192!")
                break

            resposta = pandora.get_response(entrada)
            print(f"\n{resposta}")

        except KeyboardInterrupt:
            print(f"\n{pandora.NAME}: Interrompido. Ligue 192 se for emergência.")
            break
        except Exception as e:
            print(f"\nErro: {str(e)}")
