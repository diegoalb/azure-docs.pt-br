---
title: Limites de Azure Data Box Disk | Microsoft Docs
description: Descreve os limites do sistema e tamanhos recomendados para o Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260157"
---
# <a name="azure-data-box-disk-limits"></a>Limites do Azure Data Box Disk


Considere esses limites ao implantar e operar sua solução de Microsoft Azure Data Box Disk.

## <a name="data-box-service-limits"></a>Limites de serviço do Data Box

 - O serviço Data Box está disponível nas regiões do Azure listadas na [disponibilidade da Região.](data-box-disk-overview.md#region-availability)
 - Uma única conta de armazenamento é compatível com o Data Box Disk.

## <a name="data-box-disk-performance"></a>Desempenho do Data Box Disk

Quando testado com discos conectados via USB 3.0, o desempenho do disco foi de até 430 MB/s. Os números reais variam dependendo do tamanho do arquivo usado. Para arquivos menores, o desempenho poderá ser mais baixo.

## <a name="azure-storage-limits"></a>Limites de armazenamento do Azure

Esta seção descreve os limites de serviço de Armazenamento do Azure e as convenções de nomenclatura necessárias para Arquivos do Azure, blobs de blocos do Azure e blobs de páginas do Azure, conforme aplicável ao serviço Data Box. Examine os limites de armazenamento com cuidado e siga todas as recomendações.

Para obter as informações mais recentes sobre os limites de serviço de armazenamento do Azure e práticas recomendadas para nomear compartilhamentos, contêineres e arquivos, visite:

- [Nomenclatura e referência de contêineres](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nomenclatura e referência de compartilhamentos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenções de blobs de páginas e blobs de blocos](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Se há quaisquer arquivos ou diretórios que excedem os limites de serviço de armazenamento do Azure ou não estão em conformidade com as convenções de nomenclatura de Blobs/Arquivos do Azure, esses arquivos ou diretórios não são ingeridos no Armazenamento do Azure por meio do serviço Data Box.

## <a name="data-upload-caveats"></a>Limitações de upload de dados

- Não copie dados diretamente para os discos. Copiar dados para pastas *BlockBlob,**PageBlob*e *AzureFile* pré-criadas.
- Uma pasta sob as pastas *BlockBlob* e *PageBlob* é um contêiner. Por exemplo, os contêineres são criados como *BlockBlob/contêiner* e *PageBlob/contêiner*.
- Se você tiver um objeto Azure existente (como uma bolha) na nuvem com o mesmo nome do objeto que está sendo copiado, o Data Box Disk renomeará o arquivo como arquivo(1) na nuvem.
- Todos os arquivos gravados nos compartilhamentos *BlockBlob* e *PageBlob* são carregados como um blob de blocos e um blob de páginas, respectivamente.
- Qualquer hierarquia de diretórios vazios (sem nenhum arquivo) criada sob as pastas *BlockBlob* e *PageBlob* não é carregada.
- Se há erros ao carregar dados no Azure, um log de erros é criado na conta de armazenamento de destino. O caminho para esse log de erros está disponível no portal quando o upload é concluído e você pode examinar o log para tomar uma ação corretiva. Não exclua os dados da origem sem verificar os dados carregados.
- Os metadados de arquivos e as permissões NTFS não são preservadas quando os dados são carregados para arquivos Do Zure. Por exemplo, o último atributo *modificado* dos arquivos não será mantido quando os dados forem copiados.
- Se você especificou discos gerenciados no pedido, analise as seguintes considerações adicionais:

    - Só é possível ter um disco gerenciado com um determinado nome em um grupo de recursos em todas as pastas pré-criadas e em todo o Data Box Disk. Isso implica que os VHDs enviados para as pastas pré-criadas deverão ter nomes exclusivos. Certifique-se de que o nome determinado não corresponda a um disco gerenciado já existente em um grupo de recursos. Se os VHDs tiverem nomes iguais, somente um VHD será convertido em disco gerenciado com esse nome. Os outros VHDs serão carregados como blobs de páginas na conta de armazenamento temporário.
    - Sempre copie os VHDs para uma das pastas pré-criadas. Se você copiar os VHDs fora dessas pastas ou em uma pasta que você criou, os VHDs serão carregados na conta do Armazenamento do Azure como blobs de páginas e não como discos gerenciados.
    - Apenas os VHDs fixos podem ser carregados para criar discos gerenciados. VHDs dinâmicos, VHDs diferenciais ou arquivos VHDX não têm suporte.

## <a name="azure-storage-account-size-limits"></a>Limites de tamanho da conta de armazenamento do Azure

Abaixo, são informados os limites do tamanho dos dados que são copiados para a conta de armazenamento. Verifique se os dados carregados por você estão em conformidade com esses limites. Para obter as informações mais atualizadas sobre esses limites, acesse [Destinos de escala de Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) e [Destinos de escala de Arquivos do Azure](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts).

| Tamanho dos dados copiados para a conta de armazenamento do Azure                      | Limite padrão          |
|---------------------------------------------------------------------|------------------------|
| Blob de páginas e blob de blocos                                            | 500 TB por conta de armazenamento. <br> Isso inclui dados de todas as fontes, incluindo o Data Box Disk.|


## <a name="azure-object-size-limits"></a>Limites de tamanho do objeto do Azure

Aqui estão os tamanhos dos objetos do Azure que podem ser gravados. Verifique se todos os arquivos que são carregados estão em conformidade com esses limites.

| Tipo de objeto do Azure | Limite padrão                                             |
|-------------------|-----------------------------------------------------------|
| Blob de blocos        | Aproximadamente 4,75 TiB                                                 |
| Blob de páginas         | 8 TiB <br> (Todos os arquivos carregados no formato Page Blob devem estar alinhados a 512 bytes, caso não o upload falhe. <br> Tanto o VHD quanto o VHDX estão alinhados com 512 bytes.) |
|Arquivos do Azure        | 1 TiB <br> Máx. tamanho da ação é 5 TiB     |
| Discos gerenciados     |4 TiB <br> Para obter mais informações sobre tamanho e limites, consulte: <li>[Metas de escalabilidade para discos gerenciados](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenções de nomenclatura de arquivo, blob de páginas e blob de blocos do Azure

| Entidade                                       | Convenções                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nomes de contêiner de blob de blocos e blob de páginas <br> Nomes de compartilhamento de arquivos para arquivos Azure | Precisa ser um nome DNS válido com tamanho de 3 a 63 caracteres. <br>  Deve começar com uma letra ou número. <br> Pode conter apenas letras minúsculas, números e o hífen (-). <br> Cada hífen (-) precisa ser imediatamente precedido e seguido por uma letra ou um número. <br> Hifens consecutivos não são permitidos em nomes. |
| Nomes de arquivo e diretório para arquivos do Azure     |<li> Uso de maiúsculas e minúsculas preservado, sem diferenciação. Não deve exceder 255 caracteres. </li><li> Não pode terminar com barra (/). </li><li>Se fornecido, será removido automaticamente. </li><li> Os seguintes caracteres não são permitidos:<code>" \\ / : \| < > * ?</code></li><li> Os caracteres reservados de URL precisam ser escapados corretamente. </li><li> Os caracteres inválidos para caminho de URL não são permitidos. Pontos de \\código como uE000 não são caracteres Unicode válidos. Alguns caracteres ASCII ou Unicode, como caracteres de \\controle (0x00 a 0x1F, u0081, etc.), também não são permitidos. Para saber quais são as regras que regem as cadeias de caracteres Unicode em HTTP/1.1, confira o RFC 2616, Section 2.2: Basic Rules (RFC 2616, Seção 2.2: regras básicas) e o RFC 3987. </li><li> Os nomes de arquivo a seguir não são permitidos: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, caractere de ponto (.) e de dois pontos (..).</li>|
| Nomes de blob para blobs de blocos e blobs de página      | Nomes de blob diferenciam maiúsculas de minúsculas e podem conter qualquer combinação de caracteres. <br> O nome do blob deve ter entre 1 e 1.024 caracteres. <br> Os caracteres reservados de URL precisam ser escapados corretamente. <br>O número de segmentos de caminho que incluem o nome do blob não pode exceder 254. Um segmento de caminho é a cadeia de caracteres entre caracteres delimitadores consecutivos (por exemplo, a barra '/') que correspondem ao nome de um diretório virtual. |

## <a name="managed-disk-naming-conventions"></a>Convenções gerenciadas de nomeação de disco

| Entidade | Convenções                                             |
|-------------------|-----------------------------------------------------------|
| Nomes de disco gerenciados       | <li> O nome deve ter de 1 a 80 caracteres. </li><li> O nome deve começar com uma letra ou número, terminar com uma letra, número ou sublinhado. </li><li> O nome pode conter apenas letras, números, sublinhados, períodos ou hífens. </li><li>   O nome não deve `/`ter espaços ou .                                              |

## <a name="next-steps"></a>Próximas etapas

- Revisar [os requisitos do sistema de disco da Caixa de Dados](data-box-disk-system-requirements.md)
