---
title: Continuidade de negócios em nuvem - recuperação de banco de dados
description: Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução.
keywords: continuidade dos negócios, continuidade dos negócios em nuvem, recuperação de desastre do banco de dados, recuperação de banco de dados
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 4f30bf112175742566c2957d78154e5a7abd1733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096873"
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Visão geral da continuidade dos negócios com o Banco de Dados SQL do Azure

**Continuidade dos negócios** no Banco de Dados SQL do Azure refere-se aos mecanismos, políticas e procedimentos que permitem que seu negócio continue operando em caso de interrupção, particularmente em sua infraestrutura de computação. Na maioria dos casos, o Banco de Dados SQL do Azure lidará com os eventos de interrupção que podem acontecer no ambiente de nuvem e manterá seus aplicativos e processos empresariais em execução. No entanto, existem alguns eventos disruptivos que não podem ser tratados automaticamente pelo Banco de Dados SQL, tais como:

- O usuário excluiu ou atualizou acidentalmente uma linha de uma tabela.
- Um invasor mal-intencionado conseguiu excluir dados ou remover um banco de dados.
- Terremoto causou uma interrupção de energia e datacenter temporário desabilitado.

Esta visão geral descreve os recursos que o Banco de Dados SQL do Azure fornece para a continuidade dos negócios e a recuperação de desastre. Saiba mais sobre as opções, recomendações e tutoriais para recuperação de eventos com interrupção que poderiam causar a perda dos dados ou fazer com que o banco de dados e o aplicativo se tornassem indisponíveis. Aprenda o que fazer quando um erro de usuário ou de aplicativo afeta a integridade dos dados, quando uma região do Azure tem uma interrupção ou quando seu aplicativo necessita de manutenção.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Recursos do Banco de Dados SQL que você pode usar para proporcionar a continuidade dos negócios

De uma perspectiva de banco de dados, há quatro cenários principais de interrupção em potencial:

- Falhas locais de hardware ou software que afetam o nó do banco de dados, como uma falha na unidade de disco.
- Corrupção ou exclusão de dados geralmente causada por um erro de aplicativo ou erro humano. Essas falhas são específicas do aplicativo e normalmente não podem ser detectadas pelo serviço de banco de dados.
- Interrupção do datacenter, possivelmente causada por um desastre natural. Esse cenário requer algum nível de redundância geográfica com failover de aplicativo para um datacenter alternativo.
- Erros de atualização ou manutenção, problemas não previstos que ocorrem durante a manutenção ou upgrades planejados da infra-estrutura podem exigir uma reversão rápida para um estado de banco de dados anterior.

Para mitigar as falhas locais de hardware e software, o SQL Database inclui uma arquitetura de [alta disponibilidade,](sql-database-high-availability.md)que garante a recuperação automática dessas falhas com até 99,995% de disponibilidade de SLA.  

Para proteger sua empresa contra perda de dados, o SQL Database cria automaticamente backups completos do banco de dados semanalmente, backups diferenciais de banco de dados a cada 12 horas e backups de log de transações a cada 5 a 10 minutos . Os backups são armazenados no armazenamento RA-GRS por pelo menos 7 dias para todos os níveis de serviço. Todos os níveis de serviço, exceto o período de retenção de backup configurável de suporte básico para restauração point-in-time, até 35 dias. 

O Banco de Dados SQL também fornece vários recursos de continuidade de negócios, que você pode usar para mitigar vários cenários não planejados. 

- [Tabelas temporais](sql-database-temporal-tables.md) permitem que você restaure versões de linhas de qualquer ponto no tempo.
- [Backups automatizados incorporados](sql-database-automated-backups.md) e [restauração point in Time](sql-database-recovery-using-backups.md#point-in-time-restore) permitem restaurar o banco de dados completo em algum momento dentro do período de retenção configurado até 35 dias.
- Você poderá [restaurar um banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore) para o ponto em que ele foi excluído se o **servidor do Banco de Dados SQL não tiver sido excluído**.
- [Retenção de backup de longo prazo](sql-database-long-term-retention.md) permite manter os backups em até 10 anos.
- [A replicação geográfica ativa](sql-database-active-geo-replication.md) permite criar réplicas legíveis e failover manualmente para qualquer réplica em caso de paralisação do data center ou atualização do aplicativo.
- [Grupo de failover automático](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) permite que o aplicativo seja automaticamente recuperado em caso de interrupção do data center.

## <a name="recover-a-database-within-the-same-azure-region"></a>Recuperar um banco de dados dentro da mesma região do Azure

Você pode usar backups automáticos do banco de dados para restaurar um banco de dados a um ponto no tempo passado. Dessa forma, você pode se recuperar de corrupção de dados causada por erros humanos. A restauração point-in-time permite criar um novo banco de dados no mesmo servidor que representa o estado dos dados antes do evento de corrupção. Para a maioria dos bancos de dados, as operações de restauração levam menos de 12 horas. Pode levar mais tempo para recuperar um banco de dados muito grande ou muito ativo. Para obter mais informações sobre tempo de recuperação, consulte [tempo de recuperação do banco de dados](sql-database-recovery-using-backups.md#recovery-time). 

Se o período máximo de retenção de backup suportado para o PITR (Point-in-Time Restore, restauração point-in-time) não for suficiente para o seu aplicativo, você poderá ampliá-lo configurando uma política de retenção de longo prazo (LTR) para o banco de dados. Para obter mais informações, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="compare-geo-replication-with-failover-groups"></a>Compare a replicação geográfica com grupos de failover

[Grupos de failover automático simplificam](sql-database-auto-failover-group.md#auto-failover-group-terminology-and-capabilities) a implantação e o uso da [geo-replicação](sql-database-active-geo-replication.md) e adicionam os recursos adicionais descritos na tabela a seguir:

|                                              | Replicação geográfica | Grupos de failover  |
|:---------------------------------------------| :-------------- | :----------------|
| failover automático                           |     Não          |      Sim         |
| Falha em vários bancos de dados simultaneamente  |     Não          |      Sim         |
| O usuário deve atualizar a seqüência de conexões após failover      |     Sim         |      Não          |
| Instância gerenciada suportada                   |     Não          |      Sim         |
| Pode ser na mesma região que primário             |     Sim         |      Não          |
| Múltiplas réplicas                            |     Sim         |      Não          |
| Suporta escala de leitura                          |     Sim         |      Sim         |
| &nbsp; | &nbsp; | &nbsp; |


## <a name="recover-a-database-to-the-existing-server"></a>Recuperar um banco de dados para o servidor existente

Embora seja raro, um data center do Azure pode ter uma interrupção. Quando uma interrupção ocorre, ela causa uma parada nos negócios, que pode durar alguns minutos ou horas.

- Uma opção é esperar que seu banco de dados volte a ficar online quando a interrupção do data center terminar. Isso funciona para aplicativos que podem manter o banco de dados offline. Por exemplo, um projeto de desenvolvimento ou uma avaliação gratuita não precisam funcionar constantemente. Quando um data center tiver uma interrupção, você não saberá quanto tempo ela durará. Portanto, essa opção só funcionará se o banco de dados não for necessário por um tempo.
- Outra opção é restaurar um banco de dados em qualquer servidor em qualquer região do Azure usando [backups de banco de dados com redundância geográfica](sql-database-recovery-using-backups.md#geo-restore) (restauração geográfica). A restauração geográfica usa um backup com redundância geográfica como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter está inacessível devido a uma interrupção.
- Finalmente, você pode se recuperar rapidamente de uma paralisação se tiver configurado um geosecundário usando [a georeplicação ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md) para seu banco de dados ou bancos de dados. Dependendo de sua escolha dessas tecnologias, você pode usar o failover manual ou automático. Enquanto o próprio failover leva apenas alguns segundos, o serviço levará pelo menos 1 hora para ativá-lo. Isso é necessário para garantir que o failover seja justificado pela escala da interrupção. Além disso, o failover pode resultar em pequena perda de dados devido à natureza da replicação assíncrona. 

Na medida em que você desenvolve o plano de continuidade dos negócios, será necessário compreender qual é o tempo máximo aceitável antes que o aplicativo recupere-se completamente após o evento de interrupção. O tempo necessário para a aplicação para recuperar totalmente é conhecido como Objetivo do Tempo de Recuperação (RTO). Você também precisa entender o período máximo de atualizações de dados recentes (intervalo de tempo) que o aplicativo pode tolerar perder ao se recuperar de um evento disruptivo não planejado. A perda potencial de dados é conhecida como RPO (Recovery point objective, objetivo de ponto de recuperação).

Diferentes métodos de recuperação oferecem diferentes níveis de RPO e RTO. Você pode escolher um método de recuperação específico ou usar uma combinação de métodos para obter a recuperação completa do aplicativo. A tabela a seguir compara RPO e RTO de cada opção de recuperação. Grupos de failover automático simplificam a implantação e o uso da geo-replicação e adicionam os recursos adicionais descritos na tabela a seguir.

| Método de recuperação | RTO | RPO |
| --- | --- | --- | 
| Restauração geográfica de backups replicados geograficamente | 12 h | 1 h |
| Grupos de failover automático | 1 h | 5 s |
| Failover manual do banco de dados | 30 s | 5 s |

> [!NOTE]
> *Failover manual do banco* de dados refere-se ao failover de um único banco de dados ao seu secundário geo-replicado usando o [modo não planejado](sql-database-active-geo-replication.md#active-geo-replication-terminology-and-capabilities).
Consulte a tabela no início deste artigo para obter detalhes sobre RTO e RPO de failover automático.


Use grupos de failover automático se o aplicativo atender a algum desses critérios:

- Seja crítico.
- Tenha um SLA (Contrato de Nível de Serviço) que não permita um tempo de inatividade de 12 horas ou superior.
- O tempo de inatividade pode resultar em responsabilidade financeira.
- Ter uma alta taxa de alteração de dados e 1 hora de perda de dados não é aceitável.
- Que o custo adicional da replicação geográfica ativa seja menor que a responsabilidade financeira potencial e das perdas associadas do negócio.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

Você pode optar por usar uma combinação de backups de banco de dados e geo-replicação ativa, dependendo dos requisitos do aplicativo. Para uma discussão sobre considerações de design para bancos de dados autônomos e para pools elásticos usando esses recursos de continuidade de negócios, consulte [Projete um aplicativo para recuperação de desastres em nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [estratégias de recuperação de desastres de pool elásticos.](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

As seções a seguir fornecem uma visão geral das etapas para recuperar usando os backups de banco de dados ou a replicação geográfica ativa. Para obter etapas detalhadas, incluindo requisitos de planejamento, etapas de recuperação pós-recuperação e informações sobre como simular uma paralisação para realizar uma broca de recuperação de desastres, consulte [Recuperar um Banco de Dados SQL de uma paralisação](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Prepare-se para uma interrupção

Independentemente do recurso de continuidade de negócios usados, você deve:

- Identificar e preparar o servidor de destino, incluindo as regras de firewall de IP no nível do servidor, logons e permissões de nível de banco de dados mestre.
- Determinar como redirecionar os clientes e aplicativos de cliente para o novo servidor
- Documentar outras dependências, como as configurações e alertas de auditoria

Se você não se preparar corretamente, colocar seus aplicativos online após um failover ou uma recuperação de banco de dados exigirá um tempo adicional e, provavelmente, a solução de problemas também será exigida em um momento de estresse, ou seja, uma combinação ruim.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Fazer failover em um banco de dados secundário com replicação geográfica

Se você estiver usando grupos ativos de georeplicação ou failover automático como seu mecanismo de recuperação, você pode configurar uma política de failover automático ou usar [failover manual não planejado](sql-database-active-geo-replication-portal.md#initiate-a-failover). Depois de iniciado, o failover faz com que o secundário se torne o novo primário e fique pronto para registrar novas transações e responder à consultas, com perda mínima de dados, para os dados que ainda não foram replicados. Para obter informações sobre como criar o processo de failover, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Quando o data center volta a ficar online, os primários antigos reconectam-se automaticamente ao novo primário e se tornam bancos de dados secundários. Se você precisar realocar o primário de volta para a região original, poderá iniciar um failover planejado manualmente (failback).

### <a name="perform-a-geo-restore"></a>Executar uma restauração geográfica

Se estiver usando backups automatizados com o armazenamento com redundância geográfica (habilitado por padrão), você poderá recuperar o banco de dados usando a [restauração geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). A recuperação normalmente ocorre após 12 horas, com perda de dados de até uma hora determinada pelo momento em que o último backup de log ocorreu e foi replicado. Até que a recuperação seja concluída, o banco de dados não poderá registrar nenhuma transação ou responder a qualquer consulta. Observe que a restauração geográfica só restaura o banco de dados para o último momento disponível.

> [!NOTE]
> Se o data center voltar a ficar online antes de você transferir seu aplicativo para o banco de dados recuperado, você poderá cancelar a recuperação.

### <a name="perform-post-failover--recovery-tasks"></a>Executar pós-failover / tarefas de recuperação

Após recuperar de um dos mecanismos de recuperação, você deverá executar as seguintes tarefas adicionais antes que os usuários e aplicativos entrem em funcionamento novamente:

- Redirecionar clientes e aplicativos de cliente para o novo servidor e banco de dados restaurado
- Verificar se as regras de firewall de IP do nível de servidor apropriadas estão em vigor para que os usuários se conectem ou use os [firewalls de nível de banco de dados](sql-database-firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) para habilitar as regras apropriadas.
- Verificar se os logons apropriados e as permissões nível de banco de dados mestre estão em vigor (ou usar os [usuários independentes](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable))
- Configurar a auditoria, conforme apropriado
- Configurar os alertas, conforme apropriado

> [!NOTE]
> Se você estiver usando um grupo de failover e se conectar aos bancos de dados usando o lstener de leitura / gravação, o redirecionamento após o failover ocorrerá de maneira automática e transparente no aplicativo.

## <a name="upgrade-an-application-with-minimal-downtime"></a>Atualize um aplicativo com tempo de inatividade mínimo

Às vezes, um aplicativo deve ser colocado offline devido à manutenção planejada, como uma atualização do aplicativo. [Gerenciar atualizações de aplicativos](sql-database-manage-application-rolling-upgrade.md) descreve como usar a replicação geográfica ativa para habilitar as atualizações sem interrupção do seu aplicativo em nuvem para minimizar o tempo de inatividade durante as atualizações e fornecer um caminho de recuperação caso algo saia errado.

## <a name="next-steps"></a>Próximas etapas

Para obter uma discussão sobre as considerações de design de aplicativo para bancos de dados independentes e para pools elásticos, confira [Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) e [Estratégias de recuperação de desastre para pool elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
