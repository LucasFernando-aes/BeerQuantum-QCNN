# Redes Neurais Convolucionais híbridas (Classico + Quântico) para identificação de latas defeituosas.

## Beer Quantum - Hackathon
## Grupo Bra-Beer-Ket
## Débora Fauma, Lucas Alvarenga e Marina Fernandes

Este projeto tem como objetivo o desenvolvimento de um sistema de reconhecimento de latas e/ou garrafas com falhas que venham a aparecer na linha de produção de uma cervejaria da Ambev. Em vista deste objetivo, o grupo Bra-Beer-Ket, desenvolveu esse repositório, o qual contém um modelo híbrido (Clássico + Quântico) composto por uma Rede Neural Convolucionail (do inglês, Convolutional Neural Network - CNN), usada como extrator de características, e um Classificador Quântico Variacional (do inglês, Variational Quantum Classifier - VQC), usado como meio classificador do modelo. O modelo foi treinado sobre um conjunto de dados correlato e conseguiu obter uma acurácia de aproximadamente 94% no conjunto de teste com poucas épocas de treinamento.

A seguir, alguns pontos importantes do desenvolvimento são descritos.

---

## Conjunto de Dados

Para a aprendizagem do modelo e avaliação de desempenho, encontrou-se o [conjunto de dados composto por imagens de produtos de reciclagem](web.cecs.pdx.edu/~singh/rcyc-web/index.html), categorizados em 5 classes: Caixas de Papelão, Latas, Latas amassadas, Garrafas de plástico e Garrafas de vidro. Apesar dos dados não serem específicos de produtos da Ambev, foi o conjunto mais próximo publicamente disponível na internet.

Os dados foram disponibilizados na forma de um *Array* numpy compactado. Porém, para-se ter uma idéia da estrutura e qualidade dos dados, primeiramente foi desenvolvido o notebook *recycled_dataset.ipynb*. Neste código, primeiramente abriu-se o array e separou-se os dados em cada uma de suas classes. Em seguida, algumas informações como tamanho e quantidade de imagens foram obtidas e, então, um subgrupo de imagens para cada uma das categorias foi impresso na saída do notebook. Por fim, Imagens foram novamente geradas a partir dos dados em forma de *Array* e salvas em disco em formato png.

---

## Desenvolvimento e Treinamento do Modelo

Como segunda etapa, com base no [tutorial do Pennylane](pennylane.ai/qml/demos/tutorial_quantum_transfer_learning.html) de transferência de aprendizado , iniciou-se o desenvolvimento do modelo híbrido. Como forma de extração de características das imagens, o que pode ser entendido como redução de dimensionalidade, usou-se a rede neural convolucional ResNet18 pré-treinada em um conjunto de dados mais generalizado denominado ImageNet. A ResNet é um modelo largamente adotado em problemas de visão computacional, sendo a sua versão mais simples com 18 camadas convolucionais (ResNet-18) comumente usadas em tarefas mais simples, devido a sua menor capacidade de aprendizagem (quantidade de parâmetros) quando comparada com suas versões de 50 e 151 camadas convolucionais.

O tutorial altera a última camada classificatória da ResNet-18, que sozinha contém 512000 parâmetros, para um "sanduiche" de camadas lineares (classico) com o modelo quântico VQC em seu interior, contendo, agora, apenas 2080 parametros. O modelo quântico VQC adotado pelo tutorial é constituido por 4 qubits e seu circuito tem uma profundidade de tamanho 6, primeiramente com uma superposição uniforme sobre todos os qubits e, após isso, rotações em X e portas CNOTs são recorrentemente inseridas até a profundidade desejada.

Apesar de se basear no tutorial do pennylane, a implementação do notebook main.ipynb contém algumas modificações com relação ao tutorial inicial, sendo elas:

1. O tutorial usa como exemplo um conjunto de dados de imagens de com formigas, abelhas. No nosso desenvolvimento isto foi alterado para imagens de **Latas Normais** e **Latas Amassadas**.

2. O tutorial do Pennylane concatena o *sanduíche quântico* diretamente ao modelo clássico, de tal modo que para o processamento de uma imagem ela primeiramente deve ser encaminhada pelo modelo convolucional e, só em seguida, entregue ao modelo quântico. Como o modelo clássico não está inserido no processo de otimização (daí o nome transferência de conhecimento), pode-se realizar ambas etapas de modo separado. Assim, no código desenvolvido, primeiramente as observações do dataset são encaminhadas pela rede neural convolucional e suas representações em menor dimensionalidade são obtidas, para só em seguida, um modelo com apenas o *sanduiche quântico* receber estas representações. Assim, como os modelos classicos podem ser acelerados com uso de hardwares de aceleração (*ie.* GPUs), é possível aumentar a velocidade de execução do experimento.

3. Como esta implementação é uma fase prematura do projeto, funcionando como uma **POC** (Prova de Conceito), funcionalidades que em um primeiro momento parecem irrelevantes, como salvar os modelos e impressão de gráficos, foram retiradas.

---


## Oportunidades de pesquisa e direções para trabalhos futuros

Pôde-se constatar um bom desempenho do modelo de classificação de latas, que pode ser facilmente ajustado a novos problemas de visão computacional através de um pequeno treinamento. Assim, como perspectivas futuras tem-se, por exemplo, sua generalização para um cenário completo de reciclagem de produtos da Ambev, utilização de um conjunto de dados obtido exclusivamente de produtos da ambev e sua implantação em linhas de produção. Outras frentes seria a investigação das camadas *Quanvolucionais*, que usam circuitos quânticos para a realização do processo de convolução e, com isso, criar um modelo totalmente quântico para classificação de imagens.
