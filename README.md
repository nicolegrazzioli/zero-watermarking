### **Implementação de Zero-Watermarking para Rastreabilidade Segura em Laudos Odontológicos**


#### **Problema e Justificativa**
Imagens radiológicas são dados sensíveis rigorosamente protegidos pela LGPD. Inserir marcas d'água clássicas (alteração de pixels) em documentos médicos é inaceitável, pois qualquer modificação pode ocultar patologias ou gerar falsos positivos anatômicos. Por outro lado, radiografias anonimizadas correm o risco de perda de rastreabilidade ou vazamento sem identificação de autoria.
A técnica de Zero-Watermarking (Marca d'Água Zero) garante a autenticidade e a proveniência do exame sem alterar um único pixel do arquivo original, preservando a integridade diagnóstica do paciente.


#### **Objetivos (geral e específicos)**
Desenvolver um protótipo funcional de Zero-Watermarking estrutural em Python para assegurar a rastreabilidade forense de radiografias odontológicas, mantendo integridade total da imagem original.
##### **Objetivos específicos**
1. Extrair a "impressão digital visual" dos ossos maxilares utilizando a Decomposição em Valores Singulares (SVD)
2. Garantir a anonimização dos pacientes utilizando criptografia na origem, antes da geração da chave de verificação
3. Validar a resiliência algorítmica do modelo submetendo-o a ataques geométricos degradantes

#### **Metodologia Proposta**
A Prova de Conceito (PoC) não exige hardware específico e será desenvolvida no Google Colab. As imagens de teste serão extraídas do repositório público [Dental Panoramic X-rays dataset do Kaggle](https://data.mendeley.com/datasets/hxt48yk462/2). O sistema utilizará bibliotecas padrão: `cv2 (OpenCV)` para manipulação espacial, `numpy` para cálculos matemáticos, `hashlib` para criptografia e `json` para simulação de banco de dados.

##### **Parte 1: Geração da Proteção e Anonimização**

**Desidentificação (Hash):**

- Simulação dos metadados de entrada (ex: nome = "João da Silva" e data = "07/05/2026")
- A biblioteca `hashlib.sha256()` embaralha essas strings num identificador irreversível e anonimizado (Hash)
- O pareamento real é armazenado localmente em um arquivo `dicionario.json` (Ex: `{"abc123...": "João da Silva - 07/05/2026"}`)

**Extração da Identidade da Imagem:**

- O raio-X é lido em escala de cinza (`cv2.imread(..., cv2.IMREAD_GRAYSCALE)`) e redimensionado para 256x256 pixels (`cv2.resize()`)
- O algoritmo aplica o SVD do NumPy (`numpy.linalg.svd`) para extrair a matriz com a essência estrutural profunda dos dentes
- Os valores são binarizados (0s e 1s) baseados na sua média

**Identificador Único e Registro:**

- É aplicada uma porta lógica XOR (`numpy.bitwise_xor`) entre a matriz binarizada da imagem e os bits do Hash do paciente, resultando na "Chave de Verificação", salva no arquivo `chaves_seguras.txt`
- A imagem radiológica original não sofre nenhuma manipulação nos seus pixels

##### **Parte 2: Validação e Engenharia Reversa (Simulação de Vazamento)**
- Degradação: A imagem intacta sofre degradação proposital para simular um vazamento. O arquivo é salvo com forte compressão com perdas (JPEG) ou embaçado via `cv2.GaussianBlur` (Filtro Gaussiano), resultando no arquivo `dente_vazado.jpg`

**Engenharia Reversa:**

- O sistema abre a foto `dente_vazado.jpg`, redimensiona para 256x256 e executa o SVD novamente para re-extrair a matriz do osso binarizada
- O script lê o arquivo `chaves_seguras.txt`, recupera a chave original e roda um XOR reverso com a nova matriz suja. Se a matemática resistir, o Hash criptografado (`abc123...`) é revelado
- O script varre o `dicionario.json` e imprime o alerta na tela: "Imagem vazada identificada: paciente João da Silva"


#### **Cronograma Simplificado**
- Semanas 1-2: estruturação do ambiente de desenvolvimento (Colab/Anaconda), obtenção do Dataset do Kaggle
- Semanas 3-4: Desenvolvimento da Parte 1
- Semanas 5-6: Desenvolvimento da Parte 2
- Semanas 7-8: Ajustes, análise de resiliência e entrega da documentação e protótipo finais

#### **Resultados Esperados**
Espera-se provar a superioridade e adequação do Zero-Watermarking aliado à técnica SVD para a área da saúde. A "biometria óssea visual" da radiografia deve ser capaz de sobreviver a ataques agressivos de filtro passa-baixa e perdas de frequência (JPEG). O sistema deve localizar a autoria do vazamento com velocidade, garantindo a rastreabilidade total de dados não estruturados sem causar danos à integridade do laudo clínico, cumprindo com as prerrogativas de segurança da LGPD.

#### **Referências**
- COM 3105 - NumPy (Aula 03)
- COM 3105 - Filtragem de Imagens no Domínio Espacial (Aula 08)
- COM 3105 - Compressão de Imagens e Marcas d'Água Digitais (Aula 17)
- Sentinela - Resumo e Delimitação: Infraestrutura Base vs. Contribuição Original
- Manuais de documentação nativos das bibliotecas OpenCV e NumPy
- Plataforma Kaggle

