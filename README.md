# Malicious_Macro
Desofuscação de Macro Maliciosa
### O Caso: Phishing via Macros
Durante uma análise de um e-mail suspeito, identifiquei um anexo que solicitava insistentemente a ativação de **macros**. Este é um vetor clássico de ataque que utiliza Engenharia Social para induzir o usuário a executar código malicioso localmente sob o pretexto de "visualizar o conteúdo".

<img width="932" height="592" alt="caso" src="https://github.com/user-attachments/assets/d6b74ea9-2b1e-4976-9407-aadc22ec6b8d" />

### O que são Macros e o Risco?
Macros são scripts em **VBA (Visual Basic for Applications)** integrados a documentos do Office. Embora úteis para automação de tarefas, atacantes as utilizam para: Baixar e executar payloads de servidores externos (C2). Realizar movimentação lateral na rede. Exfiltrar dados sensíveis do host infectado.

#### Ferramentas Utilizadas:
- Ubuntu Linux (Ambiente de análise)
- LibreOffice Basic IDE (Análise estática de VBA)
- Python 3 (Scripting e decodificação)
- Strings / Hexdump (Triagem inicial de binário)

---

### Investigação Técnica

Para evitar a execução do código no host, realizei uma **Análise Estática** do ficheiro `vbaProject.bin`.

#### 1. Extração do Código
Em vez de lutar contra o binário sujo no terminal (UTF-16/Bytes nulos), utilizei o editor de macros do **LibreOffice Basic** para ler o código-fonte de forma limpa e segura.
<img width="1886" height="883" alt="image" src="https://github.com/user-attachments/assets/219ad2b6-e1a3-4e79-bf55-4372ec038748" />


#### 2. Lógica de Ofuscação (XOR Cipher)
O código revelou um **Array** de números decimais ocultos. A lógica de decodificação consistia num loop que realizava uma operação **XOR** entre cada número e a sua respectiva posição (índice `i`) na lista.


#### 3. Decodificação com Python
Extraí os dados brutos e utilizei um script Python para automatizar a reversão da cifra:

```python
# Array extraído diretamente da macro maliciosa
data = [102, 109, 99, 100, 127, 100, 53, 62, 105, 57, 61, 106, 62, 62, 55, 110, 113, 114, 118, 39, 36, 118, 47, 35, 32, 125, 34, 46, 46, 124, 43, 124, 25, 71, 26, 71, 21, 88]

# Reconstrução da flag: (Número XOR Índice)
flag = "".join(chr(data[i] ^ i) for i in range(len(data)))

print(f"Flag Extraída: {flag}")

<img width="1650" height="242" alt="flag" src="https://github.com/user-attachments/assets/26521e28-e125-4894-84d2-804266a319a0" />


#### 4. Conclusão
A análise demonstrou que a melhor defesa contra ataques de macro não é apenas tecnológica, mas educacional. Como analista, a capacidade de isolar o código e entender operações bitwise fundamentais permite a rápida identificação de IoCs (Indicadores de Comprometimento) sem comprometer a segurança da estação de trabalho.
