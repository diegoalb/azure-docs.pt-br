---
title: Conceitos – dimensionar aplicativos no AKS (Serviço de Kubernetes do Azure)
description: Saiba mais sobre como dimensionar no AKS (Serviço de Kubernetes do Azure), incluindo dimensionador automático de pod horizontal, dimensionador automático do cluster e conector de Instâncias de Contêiner do Azure.
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595851"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Opções dimensionamento para aplicativos no AKS (Serviço de Kubernetes do Azure)

Conforme você executa aplicativos no AKS (Serviço de Kubernetes do Azure), precisa aumentar ou diminuir a quantidade de recursos de computação. Conforme o número de instâncias do aplicativo que você precisa alterar o número de nós do Kubernetes subjacente talvez também precise mudar. Você também pode precisar provisionar rapidamente um grande número de instâncias adicionais de aplicativos.

Este artigo apresenta os principais conceitos que ajudam você a dimensionar aplicativos no AKS:

- [Dimensionamento manual](#manually-scale-pods-or-nodes)
- [HPA (dimensionador automático de pod horizontal)](#horizontal-pod-autoscaler)
- [Dimensionador automático do cluster](#cluster-autoscaler)
- [Integração de ACI (instância de contêiner do Azure) com AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Dimensionar manualmente os pods ou os nós

Você pode dimensionar manualmente réplicas (pods) e nós para testar como seu aplicativo responde a uma alteração no estado e nos recursos disponíveis. Dimensionar recursos manualmente também permite estabelecer uma quantidade definida de recursos a serem usados para manter um custo fixo, como o número de nós. Para dimensionar manualmente, você define a contagem de réplicas ou nós. A API kubernetes então agenda a criação de pods adicionais ou nós drenantes com base nessa contagem de réplicas ou nós.

Ao reduzir os nós, a API do Kubernetes chama a API azure Compute relevante vinculada ao tipo de computação usado pelo cluster. Por exemplo, para clusters construídos na escala VM define a lógica para selecionar quais nós a remover é determinada pela API de conjuntos de escala de VM. Para saber mais sobre como os nós são selecionados para remoção em escala para baixo, consulte o [VMSS FAQ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed).

Para começar com o pods e nós de dimensionamento manual, confira [Dimensionar aplicativos no AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Dimensionador automático de pod horizontal

O Kubernetes usa o HPA (dimensionador automático de pod horizontal) para monitorar a demanda por recursos e dimensionar automaticamente o número de réplicas. Por padrão, o dimensionador automático de pod horizontal verifica a API de Métricas a cada 30 segundos para todas as alterações necessárias na contagem de réplicas. Quando são necessárias alterações, o número de réplicas aumenta ou diminui de acordo. O dimensionamento automático de pod horizontal funciona com clusters AKS que implantaram o Servidor de Métricas para o Kubernetes 1.8 ou superior.

![Dimensionamento automático de pod horizontal de Kubernetes](media/concepts-scale/horizontal-pod-autoscaling.png)

Quando você configura o dimensionador automático de pod horizontal para uma determinada implantação, pode definir os números mínimo e máximo de réplicas que podem ser executadas. Você também define a métrica para monitorar e basear quaisquer decisões de dimensionamento, como uso da CPU.

Para começar a usar o dimensionador automático de pod horizontal no AKS, confira [Dimensionamento automático de pods no AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Resfriamento de eventos de dimensionamento

Uma vez que o dimensionador automático de pod horizontal verifica a API de métricas a cada 30 segundos, eventos de escala anteriores talvez não tenham concluído com êxito antes de outra verificação ser feita. Esse comportamento poderia fazer com que o dimensionador de pod horizontal alterasse o número de réplicas antes que o evento de escala anterior pudesse receber a carga de trabalho do aplicativo e as demandas de recursos para ajustar de acordo.

Para minimizar esses eventos de corrida, os valores de recarga ou atraso são definidos. Esses valores definem quanto o dimensionador automático de pod horizontal deve esperar após um evento de escala antes que outro evento de escala possa ser disparado. Esse comportamento permite que a nova contagem de réplicas faça efeito e a API métricas reflita a carga de trabalho distribuída. Por padrão, o atraso em eventos de aumento de escala é de 3 minutos e o atraso em eventos de redução é de 5 minutos

Atualmente, você não pode ajustar esses valores de recarga do padrão.

## <a name="cluster-autoscaler"></a>Dimensionador automático do cluster

Para responder às mudanças nas demandas do pod, o Kubernetes possui um cluster autoscaler, que ajusta o número de nós com base nos recursos de computação solicitados no pool de nós. Por padrão, o cluster autoscaler verifica o servidor API métrica sacada a 10 segundos para quaisquer alterações necessárias na contagem de nós. Se o dimensionamento automático de cluster determinar que uma alteração é necessária, o número de nós no cluster do AKS aumentará ou diminuirá de acordo. O dimensionador automático de cluster funciona com clusters AKS habilitados para RBAC que executam o Kubernetes 1.10.x ou superior.

![Dimensionador automático de cluster do Kubernetes](media/concepts-scale/cluster-autoscaler.png)

O dimensionador automático do cluster normalmente é usado junto com o dimensionador automático de pod horizontal. Quando combinadas, o dimensionador automático de pod horizontal aumenta ou diminui o número de pods com base na demanda do aplicativo e o dimensionador automático de cluster ajusta o número de nós conforme necessário para executar os pods adicionais adequadamente.

Para começar a usar o dimensionador automático de cluster no AKS, confira [Dimensionador automático de cluster no AKS][aks-cluster-autoscaler].

### <a name="scale-up-events"></a>Eventos de aumento

Se um nó não tiver recursos suficientes de computação para executar um pod solicitado, esse pod não poderá progredir durante o processo de agendamento. O pod não pode começar a menos que recursos adicionais de computação estejam disponíveis no pool de nós.

Quando o cluster autoscaler nota pods que não podem ser agendados por causa de restrições de recursos de pool de nós, o número de nós dentro do pool de nós é aumentado para fornecer os recursos adicionais de computação. Quando esses nós adicionais foram implantados com sucesso e estão disponíveis para uso dentro do pool de nós, os pods são agendados para execução neles.

Se o aplicativo precisar ser dimensionado rapidamente, alguns compartimentos poderão permanecer em um estado aguardando agendamento até que os nós adicionais implantados pelo dimensionador automático de cluster possam aceitar os pods agendados. Para aplicativos que têm altas demandas de intermitência, você pode dimensionar com nós virtuais e Instâncias de Contêiner do Azure.

### <a name="scale-down-events"></a>Reduzir eventos verticalmente

O cluster autoscaler também monitora o status de agendamento de pods para nós que não receberam recentemente novas solicitações de agendamento. Este cenário indica que o pool de nós tem mais recursos de computação do que o necessário, e o número de nós pode ser diminuído.

Um nó que ultrapassa um limite por não ser mais necessário por 10 minutos por padrão é agendado para exclusão. Quando essa situação ocorre, pods são agendados para execução em outros nós dentro do pool de nós e o dimensionador automático de cluster diminui o número de nós.

Seus aplicativos podem apresentar alguma interrupção, uma vez que os pods são agendados em nós diferentes quando o dimensionador automático cluster diminui o número de nós. Para minimizar as interrupções, evite a aplicativos que usem uma instância única de pod.

## <a name="burst-to-azure-container-instances"></a>Intermitência para Instâncias de Contêiner do Azure

Para dimensionar rapidamente o cluster do AKS, você pode integrar com ACI (Instâncias de Contêiner do Azure). O Kubernetes tem componentes internos para dimensionar a contagem de nós e de réplicas. No entanto, se o aplicativo precisar dimensionar rapidamente, o dimensionador automático de pod horizontal poderá agendar mais pods, que podem ser fornecidos pelos recursos de computação existentes no pool de nós. Se configurado, esse cenário disparará o dimensionador automático de cluster para implantar nós adicionais do pool de nós, mas poderão ser necessários alguns minutos para que esses nós sejam provisionados com sucesso e permitir que o Agendador Kubernetes execute os pods neles com êxito.

![Intermitência de Kubernetes com dimensionamento para ACI](media/concepts-scale/burst-scaling.png)

ACI permite implantar rapidamente instâncias de contêiner sem sobrecarga adicional à infraestrutura. Quando você se conecta com o AKS, ACI torna-se uma extensão segura e lógica do seu cluster do AKS. O componente [de nós virtuais,][virtual-nodes-cli] que é baseado em [Virtual Kubelet,][virtual-kubelet]está instalado no seu cluster AKS que apresenta a ACI como um nó Kubernetes virtual. O Kubernetes então pode agendar pods que são executados como instâncias ACI por meio de nós virtuais, não como pods em nós de VM diretamente no cluster do AKS. Os nós virtuais estão atualmente em pré-visualização no AKS.

Seu aplicativo não requer modificação para usar os nós virtuais. As implantações podem ser dimensionadas para AKS e ACI e sem atraso, uma vez que o dimensionador automático de cluster implanta novos nós no cluster do AKS.

Nós virtuais são implantados em uma sub-rede adicional na mesma rede virtual que o seu cluster do AKS. Essa configuração de rede virtual permite que o tráfego entre ACI e AKS seja protegido. Como um cluster do AKS, uma instância ACI é um recurso de computação segura e lógica isolado de outros usuários.

## <a name="next-steps"></a>Próximas etapas

Para começar a dimensionar aplicativos, primeiro siga o [guia de início rápido para criar um cluster do AKS com a CLI do Azure][aks-quickstart]. Em seguida, você pode começar a dimensionar manual ou automaticamente aplicativos em seu cluster do AKS:

- Dimensionar manualmente os [pods][aks-manually-scale-pods] ou os [nós][aks-manually-scale-nodes]
- Use o [dimensionador automático de pod horizontal][aks-hpa]
- Use o [dimensionador automático de cluster][aks-cluster-autoscaler]

Para obter mais informações sobre os principais conceitos do Kubernetes e do AKS, confira os seguintes artigos:

- [Kubernetes / clusters AKS e cargas de trabalho][aks-concepts-clusters-workloads]
- [Kubernetes / AKS de acesso e identidade][aks-concepts-identity]
- [Kubernetes / segurança AKS][aks-concepts-security]
- [Kubernetes / redes virtuais do AKS][aks-concepts-network]
- [Kubernetes / armazenamento AKS][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md