# Relatório Técnico: Discrepância de Velocidade da Memória RAM
**Equipamento:** Lenovo Legion Slim 5 16AHP9 (Modelo 83DH)
**Data do Diagnóstico:** 07 de Abril de 2026

---

## 1. Resumo do Problema
O equipamento foi comercializado com a especificação de suporte a memórias **DDR5-5600 (5600 MT/s)**. No entanto, o sistema opera estritamente a **4800 MT/s**. Após análise profunda via software de diagnóstico de baixo nível (DMI/SMBIOS), confirmou-se que os módulos de memória instalados fisicamente são limitados a 4800 MT/s, impedindo o alcance da performance anunciada.

## 2. Evidências Coletadas (Hardware e Software)

### A. Módulos de Memória Instalados (Via `dmidecode` e `hwinfo`)
Os pentes de memória reportam as seguintes especificações nativas:
- **Part Number (Código de Peça):** `LD5DS016G-4800ST` (Fabricante: SK Hynix/Lenovo OEM)
- **Velocidade Nativa do Hardware:** 4800 MT/s
- **Velocidade Configurada atual:** 4800 MT/s
- **Voltagem de Operação:** 1.1 V (Padrão JEDEC para 4800 MT/s)
- **Capacidade:** 2x 16GB (Total 32GB)

### B. Capacidade do Processador (AMD Ryzen 7 8845HS)
- **Arquitetura:** Hawk Point (Zen 4)
- **Suporte Oficial da Controladora de Memória:** Até DDR5-5600 ou LPDDR5x-7500.
- **Status:** O processador é capaz de operar a 5600 MT/s, mas está sendo limitado pela velocidade física dos pentes de RAM instalados.

### C. Capacidade da Placa-Mãe (Lenovo 83DH)
- **SMBIOS Version:** 3.6.0
- **Maximum Capacity:** 64 GiB
- **Configuração:** 2 Slots ocupados. A BIOS está configurada para a velocidade máxima permitida pelos módulos (4800 MT/s), sem opções de "XMP" ou "EXPO" que permitiriam overclock para 5600 MT/s nesses pentes específicos.

## 3. Conclusão Técnica
Não se trata de uma configuração incorreta de software ou BIOS. O gargalo é estritamente **físico**.
- A Lenovo instalou módulos de memória com o sufixo **-4800ST**, que são validados apenas para 4800 MT/s. 
- Para atingir os 5600 MT/s prometidos no marketing do produto, seriam necessários módulos com Part Numbers compatíveis com 5600 MT/s (ex: `LD5DS016G-5600...`).

## 4. Recomendações
1. **Suporte Lenovo:** Apresentar este relatório questionando por que o produto foi entregue com memórias de 4800 MT/s se o marketing/especificação de compra indicava 5600 MT/s.
2. **Upgrade:** Caso opte por substituição manual, o sistema (Placa + CPU) aceitará pentes DDR5-5600 sem necessidade de ajustes adicionais, pois o suporte nativo existe no restante do hardware.

---
*Relatório gerado automaticamente via Gemini CLI Diagnostics.*
