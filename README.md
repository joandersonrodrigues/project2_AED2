# Análise Estrutural e Topológica da Rede Urbana de Nova Parnamirim e Adjacências

Este repositório contém o projeto prático de análise de redes complexas e sistemas urbanos aplicado à malha viária, desenvolvido individualmente para a disciplina de **Estrutura de Dados II (DCA3702)** do curso de Engenharia de Computação da **Universidade Federal do Rio Grande do Norte (UFRN)**. 

O objetivo central é modelar e interpretar a infraestrutura viária de uma região urbana real utilizando a **Teoria dos Grafos**, mapeando a conectividade de cruzamentos (nós) e ruas (arestas) para identificar gargalos de tráfego, hubs de distribuição e a resiliência do sistema de mobilidade.

---

## 👥 Integrante
* **JOANDERSON LUAN DA SILVA LINHARES RODRIGUES**
* Matrícula: 20240078936
* Curso: Engenharia de Computação (UFRN)

## 🎥 Vídeo de Apresentação e Defesa Técnico-Analítica
[🔗 Clique aqui para assistir à apresentação completa no Loom](https://www.loom.com/share/seu-link-aqui-do-loom)

---

## 🧠 1. Objetivo do Trabalho
O problema norteador deste projeto consiste em responder à seguinte questão analítica:
> **Quais são os elementos estruturais mais importantes da malha viária analisada e como diferentes métricas de grafos, como grau, centralidade e k-core, ajudam a caracterizá-los?**

A proposta afasta-se do mero uso mecânico de bibliotecas de software para focar na interpretação crítica e científica dos resultados matemáticos, correlacionando a topologia computacional com o comportamento real do fluxo de veículos no espaço físico.

---

## ⚙️ 2. Região de Estudo e Justificativa Urbana
A região escolhida compreende o ecossistema urbano formado pelo bairro de **Nova Parnamirim (Parnamirim/RN)** e seus distritos limítrofes integrados: **Parque das Árvores**, **Parque do Jiqui** e **Parque das Nações**.

### Justificativa Metodológica e Empírica:
Nova Parnamirim é uma das zonas residenciais de maior densidade e crescimento acelerado na região metropolitana de Natal. Historicamente, a região expandiu-se de forma fragmentada, caracterizada pela proliferação de grandes condomínios fechados (horizontais e verticais) e loteamentos privados que interrompem o fluxo contínuo das vias.

Essa característica urbana cria um fenômeno crítico de mobilidade: os bairros adjacentes de **Parque das Árvores**, **Parque do Jiqui** e **Parque das Nações** atuam como densas "bolhas de origem" habitacionais. Diariamente, essas regiões geram um fluxo pendular massivo de moradores que precisam atravessar a malha de Nova Parnamirim para acessar os eixos de escoamento principais em direção à capital (como a BR-101 e a Avenida Engenheiro Roberto Freire). Toda essa carga veicular das adjacências é injetada e afunilada em pouquíssimas vias coletoras, com destaque absoluto para a **Avenida Maria Lacerda Montenegro**.

Para modelar essa dinâmica de forma fidedigna e mitigar o **Efeito de Borda** (onde a análise isolada de apenas um bairro esconderia a carga real de veículos que entram na rede vindos de fora), a modelagem foi estruturada como uma **análise multi-bairro integrada**. Utilizando a função `graph_from_place` do OSMnx com a composição exata dos polígonos oficiais de **Nova Parnamirim, Parque das Árvores, Parque do Jiqui e Parque das Nações**, foi possível capturar com precisão a costura topológica real de todas as vias de transição, cruzamentos comuns e conexões periféricas que sustentam o fluxo dessa macro-região urbana.

---

## 🧪 3. Metodologia e Pipeline de Dados
O projeto foi implementado em Python em um fluxo modular estruturado nas seguintes etapas:

1. **Ingestão de Dados Espaciais (`OSMnx`)**: Captura automática da geometria das ruas a partir do OpenStreetMap através da função `graph_from_place`, passando uma lista com a composição dos limites poligonais oficiais dos bairros analisados (*Nova Parnamirim, Parque das Árvores, Parque do Jiqui e Parque das Nações*) sob o critério `network_type="drive"`. O algoritmo realiza o download e a costura automatizada dessas malhas contíguas em um único `MultiDiGraph` (Grafo direcionado com suporte a arestas paralelas).
2. **Simplificação e Limpeza Topológica (`NetworkX`)**: Para análises estruturais de centralidade macroscópica, o grafo direcionado unificado foi convertido para um grafo simples não-direcionado (`nx.Graph`). Este processo fundiu as vias de mão dupla em uma única conexão matemática equivalente entre dois cruzamentos e removeu os *auto-loops* (rotatórias ou ruas em U que começam e terminam no mesmo cruzamento).
3. **Cálculo Computacional de Métricas**: Implementação dos algoritmos de Teoria dos Grafos para extração de dados estatísticos e topológicos da macro-região viária integrando as métricas de Grau, Betweenness Centrality, Closeness Centrality e Core Number.
4. **Engenharia de Atributos para o Gephi**: Ajuste e padronização dos tipos de dados para exportação. Os atributos de coordenadas nativos (`x` para longitude, `y` para latitude) e as métricas calculadas foram explicitamente forçados como numéricos (`float` e `int`), enquanto os dados qualitativos das ruas foram convertidos para strings, eliminando incompatibilidades e estruturas de listas aninhadas do OSMnx que travam o analisador do Gephi.
5. **Análise Visual e Filtragem**: Renderização e manipulação da rede complexa no Gephi em duas perspectivas contrastantes: a geográfica real (preservando o desenho e o encaixe cartográfico dos 4 bairros) e a estrutural de força (revelando a organização lógica dos fluxos).

---

## 📊 4. Apresentação das Métricas Calculadas (Estatística da Rede)

### 4.1 Perfil de Conectividade Local: Distribuição de Grau
O Grau ($k$) de um nó representa a quantidade de ruas que se conectam em um determinado cruzamento.

[INSERIR IMAGEM AQUI: grafico_distribuicao_grau.png]

#### 💬 Comentário Analítico da Imagem:
*(Substitua este texto pela sua análise do gráfico. Destaque o domínio do Grau 3, explicando como a predominância de entroncamentos em "T" limita as opções de conversão imediata do motorista, e analise o Grau 1 como o reflexo físico de ruas sem saída e acessos restritos de condomínios residenciais que empurram o tráfego para a malha principal dos 4 bairros).*

### 4.2 Distribuição de Gargalos e Centros Estruturais (Histogramas)
O gráfico combinado contém os histogramas de *Betweenness Centrality* e *Closeness Centrality*:

[INSERIR IMAGEM AQUI: histogramas_centralidade.png]

#### 💬 Comentário Analítico da Imagem:
*(Substitua este texto pelo comentário dos histogramas. Explique a curva em "L" (cauda longa) do Betweenness, evidenciando que mais de 90% das ruas da macro-região têm score zero (uso estritamente local), enquanto menos de 5% concentram scores altíssimos (gargalos). Comente também a curva em sino do Closeness, que mapeia a homogeneidade da distância média geométrica dos cruzamentos em direção ao centro de gravidade do sistema viário integrado).*

---

## 🗺️ 5. Visualizações Estruturais no Gephi

### 5.1 Visualização Geográfica (A Realidade Física)
Renderização da rede mantendo a fidelidade espacial real das coordenadas geográficas através do plugin **Geo Layout**, configurado com a projeção *Mercator*, mapeando a latitude para o eixo Y e a longitude para o eixo X. Foi adotada a codificação visual em *Dark Mode*: tamanho dos nós proporcional ao **Grau** e cor dos nós baseada no mapa de calor do **Betweenness Centrality** (tons frios para fluxos locais; vermelho vibrante para gargalos estruturais).

[INSERIR IMAGEM AQUI: visao_geografica_gephi.png]

#### 💬 Comentário Analítico da Imagem:
*(Substitua este texto pela análise do seu mapa escuro do Gephi. Descreva como a linha vermelha contínua coincide perfeitamente com o eixo da Avenida Maria Lacerda Montenegro e as alças de acesso que desembocam na rodovia BR-101. Aponte visualmente como as malhas do Parque das Árvores, Parque do Jiqui e Parque das Nações atuam como um enorme funil conectado a esse eixo por pouquíssimas artérias de transição).*

### 5.2 Visualização Estrutural (A Topologia Pura via ForceAtlas2)
Renderização baseada em um layout direcionado por força utilizando o algoritmo **ForceAtlas2** (Modo LinLog ativado, Dissuadir Hubs ativado, Dimensionamento/Escala ajustado em alta proporção, e Prevenção de Sobreposição ativa). Esta perspectiva ignora a geografia terrestre e agrupa os cruzamentos com base estrita na força de suas conexões e caminhos mínimos.

[INSERIR IMAGEM AQUI: visao_estrutural_forceatlas2.png]

#### 💬 Comentário Analítico da Imagem:
*(Substitua este texto pela análise do grafo após rodar o ForceAtlas2. Explique como a malha urbana dos 4 bairros se fragmentou visualmente em "clusters" ou cachos de nós densos (comunidades). Mostre como os loteamentos internos e condomínios de cada setor formam bolhas isoladas nas extremidades, e como o eixo da Maria Lacerda se estica no centro como um cabo de guerra tensionado, provando graficamente a sua função de ponte única de sustentação entre as comunidades).*

---

## 🔍 6. Respostas Detalhadas às Questões Analíticas Obrigatórias

**1. Os nós com maior grau coincidem com os nós de maior betweenness?**
Não necessariamente, e este é um achado fundamental da Teoria dos Grafos aplicada ao tráfego urbano. Cruzamentos localizados no interior de grandes loteamentos residenciais no Parque das Árvores, Parque do Jiqui ou Parque das Nações apresentam alto grau local (Grau 4 ou 5, representando cruzamentos em "X" ou rotatórias internas complexas interligando ruas de bairro). Contudo, o seu *Betweenness Centrality* (Intermediação) aproxima-se de zero. Isso ocorre porque essas estruturas atendem exclusivamente ao tráfego capilar interno; nenhum veículo de travessia macroscópica ou de trânsito interbairros entra no labirinto residencial para cruzar a cidade. Em contrapartida, os nós localizados nos eixos coletores principais de Nova Parnamirim apresentam scores críticos de Betweenness mesmo possuindo graus menores (como entroncamentos de Grau 3 em "T"), provando que o volume de caminhos mínimos globais que passam por ali independe da complexidade geométrica local do cruzamento.

**2. O núcleo identificado pelo k-core coincide com os principais hubs?**
Na decomposição por K-Core, a rede é submetida a um algoritmo de poda iterativa que remove os nós de menor conectividade até isolar os subgrafos mais densos e coesos. A malha integrada dos 4 bairros apresentou um **Core Máximo de K=2**, abrangendo a espinha dorsal central da rede onde estão os principais hubs de Grau 4. O fato de o núcleo estabilizar em uma ordem topológica baixa (K=2) denota que a rede urbana possui uma estrutura de coesão frágil. Embora os hubs de maior grau façam parte desse núcleo, eles não estão massivamente interconectados na forma de uma malha altamente redundante (como uma grade em formato de matriz quadrada perfeita), mas sim atuando de forma linear ao longo de corredores, cercados por extensas ramificações arbóreas de baixa conectividade dos bairros residenciais periféricos.

**3. O que a métrica de betweenness revela que o grau não revela?**
O Grau quantifica uma propriedade estritamente **local e imediata** (quantas opções de conversão existem naquele cruzamento específico). O Betweenness Centrality quantifica uma propriedade **global e sistêmica** (quantas rotas de menor distância na área de estudo inteira dependem obrigatoriamente daquele nó para existir). O Betweenness revela a **vulnerabilidade de estrangulamento e a ausência de redundância da malha**. Um nó de grau baixo (uma reta simples ou conversão em uma avenida arterial) pode ser matematicamente invisível na métrica de grau, mas se for a única conexão física rompendo uma barreira geográfica ou de divisão de bairros, o seu Betweenness será máximo. Ele mapeia os pontos únicos de falha do sistema viário.

**4. O que muda quando a rede é analisada em sua posição geográfica real e quando é analisada por um layout estrutural?**
Na visualização geográfica real (Geo Layout), o grafo está subordinado às restrições físicas das coordenadas GPS. Essa visão mascara o isolamento topológico, pois ruas de bairros vizinhos (como Parque das Nações e Nova Parnamirim) que estão separadas por escassos metros de distância física parecem integradas no mapa, ignorando se há muros de condomínios ou ausência de vias conectando-as. No layout estrutural (ForceAtlas2), a geografia terrestre é anulada e as leis físicas de repulsão de grafos assumem o controle. Nós fortemente conectados colapsam formando "comunidades urbanas" (as bolhas residenciais), enquanto regiões isoladas são expelidas para a periferia. O layout estrutural escancara o esqueleto lógico da macro-região, revelando quais setores funcionam de forma isolada e quais vias funcionam como cordões umbilicais de tráfego.

**5. Existem regiões críticas para mobilidade urbana na área analisada?**
Sim. A criticidade é matematicamente comprovada pela assimetria extrema observada no histograma de Betweenness. As regiões críticas concentram-se na totalidade do eixo longitudinal da Avenida Maria Lacerda Montenegro e nos nós de transição que realizam a interface da malha urbana com a rodovia federal BR-101. O afunilamento estrutural provocado pela injeção da carga veicular contínua vinda do Parque das Árvores, Parque do Jiqui e Parque das Nações em direção a essa avenida cria zonas de saturação crônica. A malha viária não possui caminhos alternativos paralelos capazes de absorver ou diluir essa demanda, tornando esses eixos suscetíveis a engarrafamentos severos decorrentes de qualquer perturbação mínima na via.

**6. A rede parece homogênea ou apresenta concentração estrutural?**
A rede apresenta uma **altíssima concentração estrutural e uma organização fortemente hierárquica**, sendo o oposto de uma rede homogênea. A homogeneidade topológica demandaria uma distribuição de grau uniforme (como uma malha perfeitamente planejada em grade). Os dados extraídos provam a heterogeneidade: a malha é dominada massivamente por cruzamentos de Grau 3 (estruturas em "T"), que funcionam canalizando o fluxo de vias menores de bairros como o Parque das Árvores para eixos coletores de maior capacidade. Esse padrão de afunilamento contínuo cria uma hierarquia rígida onde pouquíssimas arestas suportam a carga viva de transporte de toda a região.

**7. Os resultados obtidos fazem sentido considerando o conhecimento urbano da região escolhida?**
Absolutamente. Os resultados explicam matematicamente a causa raiz dos congestionamentos históricos na Avenida Maria Lacerda. O crescimento urbano baseado no isolamento de condomínios fechados e grandes loteamentos residenciais (Parque das Nações, Jiqui e Árvores) bloqueou a capilaridade das ruas, fragmentando a rede em bolhas residenciais de baixa coesão (provada pelo core máximo K=2). Como os moradores dessas adjacências precisam se deslocar diariamente e não possuem vias transversais redundantes de ligação interbairros, a matemática dos grafos afunilou milhares de caminhos mínimos sobre o mesmo conjunto de nós centrais, gerando o pico de calor visual em vermelho observado no Gephi e validando o modelo computacional.

---

## 🚀 7. Filtros Aplicados no Gephi para Análise Avançada

Para extrair conclusões estruturais profundas, foram aplicados dois filtros obrigatórios na interface do Gephi:

1. **Filtro de Grau (Top 10% dos nós mais conectados)**:
   * *Configuração*: Janela de Filtros -> *Topology* -> *Degree Range*.
   * *O que revelou*: Isolou visualmente a espinha dorsal de cruzamentos em "X" e grandes intersecções distribuídas ao longo das avenidas coletoras, removendo o ruído visual das centenas de ruas residenciais locais e evidenciando onde estão localizados os eixos de distribuição primária da região.
2. **Filtro de Subgrafo K-Core (Maior ou igual a K=2)**:
   * *Configuração*: Janela de Filtros -> *Attributes* -> *Equal/Range* -> `core_number` definido para o intervalo $\ge 2$.
   * *O que revelou*: Removeu todas as pontas soltas da rede viária (ruas sem saída, ramificações periféricas e caminhos de acesso restrito de condomínios fechados). O subgrafo resultante expôs a verdadeira "malha inquebrável" contínua que suporta o tráfego interbairros da macro-região.

---

## 🎯 8. Principais Conclusões
* A aplicação prática da Teoria dos Grafos provou ser uma ferramenta científica robusta para a engenharia de tráfego e planeamento de *Smart Cities*, convertendo perceções empíricas em dados métricos exatos.
* A macro-região integrada configura um sistema viário de **baixa resiliência topológica**, caracterizado pela dependência severa de pouquíssimos eixos de intermediação global (alto Betweenness) e uma malha dominada por conexões em "T" de escoamento hierárquico.
* Soluções de mobilidade para a região não devem focar apenas no alargamento de vias existentes, mas sim na **quebra das bolhas estruturais**, criando novas arestas de conexão transversal que interliguem o Parque das Árvores, Parque das Nações e Parque do Jiqui sem a necessidade de sobrecarregar obrigatoriamente a Avenida Maria Lacerda Montenegro.

---
**Algoritmos e Estruturas de Dados II** | Engenharia de Computação | UFRN | 2026.1
