# NVIDIA Xid 154 Checklist

Contexto atual:

- Notebook: Lenovo Legion Slim 5 16AHP9 (`83DH`)
- BIOS vista no bug report: `NRCN23WW` (`03/14/2025`)
- Linux atual: kernel `6.19.9-1-default`
- Driver NVIDIA Linux: `580.142`
- Sintoma principal no Linux: `NVRM: Xid 154`
- O problema aconteceu em `X11` e `Wayland`
- O problema aconteceu com e sem monitor externo
- O problema aconteceu em `dGPU only` e `hybrid auto`
- Remover `amdgpu.sg_display=0` e `amdgpu.dcdebugmask=0x10` nao resolveu

Objetivo agora:

- Ver se a NVIDIA tambem apresenta falha no Windows
- Ver se ha BIOS/firmware mais nova
- Distinguir bug de software Linux de problema de firmware/hardware

## No Windows

1. Abrir o Lenovo Vantage e verificar:
- atualizacao de BIOS/UEFI
- atualizacao de firmware/EC
- atualizacao de chipset/GPU recomendada pela Lenovo
- modos graficos disponiveis (`Hybrid`, `dGPU only`, `Auto`, `Advanced Optimus`, etc.)
- modos de energia/performance que afetem a GPU

2. Testar com o monitor externo conectado:
- usar o sistema normalmente por alguns minutos
- abrir navegador com aceleracao grafica
- reproduzir video
- abrir algo com uso 3D, se possivel

3. Se possivel, fazer um teste de carga leve/moderada na GPU:
- 3DMark
- Unigine Superposition
- OCCT
- ou algum jogo/aplicacao 3D que voce ja tenha

4. Observar sintomas:
- tela preta
- congelamento
- reset do driver
- monitor externo apagando
- artefatos
- reboot necessario

## Event Viewer no Windows

Abrir:

- `Event Viewer`
- `Windows Logs`
- `System`

Filtrar ou procurar por:

- `nvlddmkm`
- `Display`
- `WHEA-Logger`
- `Kernel-Power`

Mensagens importantes:

- `Display driver nvlddmkm stopped responding and has recovered`
- erros recorrentes de `nvlddmkm`
- eventos `WHEA-Logger` relacionados a PCIe/GPU

## O que anotar

Anotar estas respostas:

- O problema apareceu no Windows?
- Apareceu so com monitor externo ou tambem sem ele?
- A Lenovo ofereceu BIOS/firmware novo?
- O Event Viewer mostrou erros de `nvlddmkm`?
- O Event Viewer mostrou `WHEA-Logger`?
- Alguma mudanca de modo grafico no Vantage/BIOS alterou o comportamento?

## Interpretacao

Se der problema tambem no Windows:

- sobe bastante a suspeita de firmware ou hardware
- BIOS/EC vira prioridade
- se continuar igual apos BIOS nova, considerar assistencia/RMA

Se nao der problema no Windows:

- reforca bug especifico da pilha Linux/NVIDIA nessa maquina
- proximo passo: reportar com `nvidia-bug-report.log.gz`

## Ao voltar para o Linux

Rodar:

```bash
uname -r
echo "$XDG_SESSION_TYPE"
sudo journalctl -b 0 -k -p warning..alert --no-pager | rg 'NVRM|Xid|nvidia|amdgpu|drm'
```

E trazer junto:

- resumo do que mudou no BIOS/Vantage
- se o Windows apresentou ou nao sintomas
- qualquer erro relevante do Event Viewer
