# Controle de Temperatura por Escalonamento (FC)

Este repositório contém a lógica de controle de potência para o aquecimento industrial do Forno à Indução, implementada em S7-STL (Statement List) para CLPs Siemens (S7-300/400).

## 📝 Descrição do Projeto

O bloco FC foi desenvolvido para substituir ou complementar controles PID em sistemas que exigem uma resposta de potência baseada em degraus (passos discretos). A lógica calcula a distância entre a temperatura atual e o Setpoint, aplicando uma rampa de potência proporcional ao número de degraus configurados.



## 🚀 Funcionalidades

* Rampa Proporcional: Transição suave entre Potência Nominal e Potência de Sustentação.
* Controle de Banda: Início automático do escalonamento ao atingir o offset de temperatura configurado.
* Proteção de Limite: Lógica dedicada para controle fino caso a temperatura ultrapasse o Setpoint em até 0.5°C.
* Tratamento de Dados: Conversão de cálculos REAL para saída analógica INT com arredondamento e filtro contra valores negativos.

---

## 🛠️ Estrutura de Variáveis

### Entradas (Inputs)
| Variável | Tipo | Descrição |
| :--- | :--- | :--- |
| r_Pot_Nominal | REAL | Potência máxima de aquecimento (100%). |
| r_Pot_Sustentacao | REAL | Potência necessária para manter o encharque térmico. |
| r_PV_Temp | REAL | Process Value: Temperatura lida pelo sensor. |
| r_SP_Temp | REAL | Setpoint: Temperatura desejada. |
| r_Band_Ctrl | REAL | Diferença de temperatura para iniciar o escalonamento. |
| r_Qtd_Degraus | REAL | Quantidade de divisões da rampa de potência. |

### Saídas (Outputs)
| Variável | Tipo | Descrição |
| :--- | :--- | :--- |
| i_Out_Analog | INT | Valor final para o canal analógico (PQW). |

### Variáveis Temporárias (Internal)
As variáveis de cálculo interno utilizam o padrão CAIXA ALTA para fácil identificação no código:
* R_TEMP_START_CTRL, R_DIFF_PV_SP, R_DELTA_POTENCIA, R_CALC_OUT_REAL, etc.

---

## 💻 Exemplo da Lógica (STL)

`stl
// CÁLCULO DO PONTO DE INÍCIO DO CONTROLE
      L     #r_SP_Temp
      L     #r_Band_Ctrl
      -R    
      T     #R_TEMP_START_CTRL

// VERIFICAÇÃO SE ESTÁ DENTRO DA FAIXA DE CONTROLE
      L     #r_PV_Temp
      L     #R_TEMP_START_CTRL
      >R    
      JC    M003
