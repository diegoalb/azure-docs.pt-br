---
title: Usar Importação/Exportação do Microsoft Azure para transferir dados para Blobs do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação e exportação no portal do Azure para transferir dados de e para Blobs do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282487"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usar o serviço de importação/exportação do Microsoft Azure para importar dados do Armazenamento de Blobs

Este artigo fornece instruções passo a passo sobre como usar o serviço de Importação/Exportação do Microsoft Azure para importar com segurança grandes quantidades de dados do Armazenamento de Blobs. Para importar dados para Blobs do Azure, o serviço exige o envio de unidades de disco criptografadas contendo dados para um datacenter do Azure.  

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados ao Armazenamento de Blobs, revise cuidadosamente e preencha a seguinte lista de pré-requisitos para esse serviço.
Você deve:

* Ter uma assinatura ativa do Azure que pode ser usada para o serviço de Importação/Exportação.
* Ter pelo menos uma conta do Armazenamento do Azure com um contêiner de armazenamento. Consulte a lista de [Contas de armazenamento e tipos de armazenamento com suporte para o serviço de Importação/Exportação](storage-import-export-requirements.md).
  * Para obter informações sobre como criar uma nova conta de armazenamento, consulte [Como criar uma conta de armazenamento](storage-account-create.md).
  * Para obter informações sobre o contêiner de armazenamento, acesse [Criar um contêiner de armazenamento](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Ter o número adequado de discos de [Tipos com suporte](storage-import-export-requirements.md#supported-disks).
* Ter um sistema Windows executando uma [Versão do sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
* Habilite o BitLocker no sistema Windows. Consulte [Como habilitar o BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
* [Baixe a versão 1 do WAImportExport mais recente](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows. A versão mais recente da ferramenta tem atualizações de segurança para permitir um protetor externo para a tecla BitLocker e o recurso de modo de desbloqueio atualizado.

  * Descompacte para a pasta padrão `waimportexportv1`. Por exemplo, `C:\WaImportExportV1`.
* Ter uma conta FedEx/DHL. Se você quiser usar uma operadora diferente da FedEx/DHL, entre `adbops@microsoft.com`em contato com a equipe de operações da Caixa de Dados do Azure em .  
  * A conta deve ser válida, deve ter saldo e ter recursos de devolução.
  * Gerar um número de controle para o trabalho de exportação.
  * Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de controle.
  * Se você não tiver uma conta de transportadora, vá para:
    * [Criar uma conta FedEX](https://www.fedex.com/en-us/create-account.html), ou
    * [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Essa etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas como número de série da unidade, chave de criptografia e detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1. Conecte as unidades de disco ao sistema Windows via conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra da unidade ao volume. Não use pontos de montagem.
3. Habilite a criptografia BitLocker no volume NTFS. Se estiver usando um Windows Server System, use as instruções em [Como habilitar o BitLocker no Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/).
4. Copie os dados para o volume criptografado. Use arrastar e soltar, Robocopy ou qualquer outra ferramenta de cópia. Um arquivo journal *(.jrn)* é criado na mesma pasta onde você executa a ferramenta.

   Se a unidade estiver bloqueada e você precisar desbloquear a unidade, as etapas de desbloqueio podem ser diferentes dependendo do seu caso de uso.

   * Se você adicionou dados a uma unidade pré-criptografada (a ferramenta WAImportExport não foi usada para criptografia), use a chave BitLocker (uma senha numérica que você especifica) no popup para desbloquear a unidade.

   * Se você adicionou dados a uma unidade criptografada pela ferramenta WAImportExport, use o seguinte comando para desbloquear a unidade:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Abra um PowerShell ou janela de linha de comando com privilégios administrativos. Para alterar o diretório para a pasta descompactada, execute o comando a seguir:

    `cd C:\WaImportExportV1`
6. Para obter a chave do BitLocker da unidade, execute o comando a seguir:

    `manage-bde -protectors -get <DriveLetter>:`
7. Para preparar o disco, execute o comando a seguir. **Dependendo do tamanho dos dados, isso pode levar de várias horas a dias.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Um arquivo de diário é criado na mesma pasta em que você executou a ferramenta. Dois outros arquivos também são criados - um arquivo *.xml* (pasta onde você executa a ferramenta) e um arquivo *drive-manifest.xml* (pasta onde os dados residem).

    Os parâmetros utilizados são descritos na tabela a seguir:

    |Opção  |Descrição  |
    |---------|---------|
    |/j:     |O nome do arquivo de diário, com a extensão .jrn. Um arquivo de diário é gerado por unidade. É recomendável utilizar o número de série do disco como o nome do arquivo de diário.         |
    |/id:     |A ID da sessão. Use um número de sessão exclusivo para cada instância do comando.      |
    |/t:     |A letra da unidade do disco a ser enviado. Por exemplo, unidade `D`.         |
    |/bk:     |A chave do BitLocker para a unidade. O código de acesso da saída de `manage-bde -protectors -get D:`      |
    |/srcdir:     |A letra da unidade do disco a ser enviado seguida por `:\`. Por exemplo, `D:\`.         |
    |/dstdir:     |O nome do contêiner de destino no Armazenamento do Microsoft Azure.         |
    |/blobtype:     |Esta opção especifica o tipo de blobs para os quais você deseja importar os dados. Para blobs de `BlockBlob` bloco, isto é e `PageBlob`para bolhas de página, é .         |
    |/skipwrite:     |A opção que especifica que não há novos dados necessários para serem copiados e que os dados existentes no disco devem ser preparados.          |
    |/habilitar contentmd5:     |A opção, quando ativada, garante que o MD5 seja computado e definido como `Content-md5` propriedade em cada bolha. Use esta opção somente se `Content-md5` quiser usar o campo depois que os dados forem carregados no Azure. <br> Essa opção não afeta a verificação de integridade dos dados (que ocorre por padrão). A configuração aumenta o tempo de upload de dados para a nuvem.          |
8. Repita a etapa anterior para cada disco que precisa ser enviado. Um arquivo de diário com o nome fornecido é criado para cada execução da linha de comando.

    > [!IMPORTANT]
    > * Juntamente com o arquivo de diário, um arquivo `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também é criado na mesma pasta em que a ferramenta reside. O arquivo .xml é usado no lugar do arquivo de diário ao criar um trabalho, se o arquivo de diário for muito grande.

## <a name="step-2-create-an-import-job"></a>Etapa 2: criar um trabalho de importação

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.

1. Faça logon em https://portal.azure.com/.
2. Vá para **Todos os serviços > Armazenamento > Trabalhos de importação/exportação**.

    ![Vá para Trabalhos de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique **em Criar trabalho de importação/exportação**.

    ![Clique em Criar Trabalho de Importação/Exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Em **Noções básicas**:

   * Selecione **Importar para o Azure**.
   * Digite um nome descritivo para o trabalho de importação. Use o nome para acompanhar o andamento dos trabalhos.
       * O nome pode conter apenas letras minúsculas, números e hifens.
       * O nome deve começar com uma letra e não pode conter espaços.
   * Selecione uma assinatura.
   * Insira ou selecione um grupo de recursos.  

     ![Criar o trabalho de importação - Etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. Em **Detalhes do trabalho**:

   * Carregue os arquivos de diário da unidade que você obteve durante a etapa de preparação da unidade. Se utilizou `waimportexport.exe version1`, envie um arquivo para cada unidade que você preparou. Se o tamanho do arquivo de diário exceder 2 MB, você poderá usar o `<Journal file name>_DriveInfo_<Drive serial ID>.xml` também criado com o arquivo de diário.
   * Selecione a conta de armazenamento de destino onde os dados residirão.
   * O local final da corrida é preenchido automaticamente com base na região da conta de armazenamento selecionada.

   ![Criar trabalho de importação - Etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. Em **Informações sobre a remessa de devolução**:

   * Selecione a operadora na lista suspensa. Se você quiser usar uma operadora diferente da FedEx/DHL, escolha uma opção existente a partir da queda. Entre em contato com a `adbops@microsoft.com` equipe de operações da Caixa de Dados do Azure com as informações sobre a operadora que você pretende usar.
   * Insira um número válido de conta de operadora que você criou com essa operadora. A Microsoft usará essa conta para enviar de volta as unidades para você após a conclusão do seu trabalho de importação. Caso não tenha um número de conta, crie uma conta da operadora [FedEx](https://www.fedex.com/us/oadr/) ou [DHL](https://www.dhl.com/).
   * Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/município e país/região.

       > [!TIP]
       > Em vez de especificar um endereço de email para um usuário único, forneça um email de grupo. Isso garante que você receba notificações mesmo que um administrador saia.

     ![Criar o trabalho de importação - Etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. No **Resumo**:

   * Revise as informações do trabalho fornecidas no resumo. Anote o nome do trabalho e o endereço de remessa do datacenter do Azure para enviar os discos de volta ao Azure. Essas informações serão utilizadas posteriormente na etiqueta de remessa.
   * Clique em **OK** para criar o trabalho de importação.

     ![Criar o trabalho de importação - Etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Passo 3 (Opcional): Configure a chave gerenciada pelo cliente

Pule este passo e vá para o próximo passo se quiser usar a chave gerenciada pela Microsoft para proteger as teclas do BitLocker para as unidades. Para configurar sua própria chave para proteger a chave BitLocker, siga as instruções em [Configurar chaves gerenciadas pelo cliente com o Azure Key Vault para importação/exportação do Azure no portal Azure](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Passo 4: Enviar as unidades

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Passo 5: Atualize o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Passo 6: Verifique o upload de dados para o Azure

Acompanhe o trabalho até a conclusão. Quando o trabalho estiver concluído, verifique se os dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi realizado com êxito.

## <a name="next-steps"></a>Próximas etapas

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Verificar os requisitos de Importação/Exportação](storage-import-export-requirements.md)
