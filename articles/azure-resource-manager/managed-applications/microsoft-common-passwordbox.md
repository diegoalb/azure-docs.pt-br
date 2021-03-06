---
title: Elemento de UI passwordbox
description: Descreve o elemento de interface do usuário Microsoft.Common.PasswordBox para o portal do Azure. Permite que os usuários forneçam um valor secreto ao implantar aplicativos gerenciados.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652301"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.PasswordBox

Um controle que pode ser usado para fornecer e confirmar uma senha.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Comentários

- Esse elemento não dá suporte à propriedade `defaultValue`.
- Para obter detalhes sobre a implementação de `constraints`, consulte [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se `options.hideConfirmation` for definido como **true**, a segunda caixa de texto para confirmar a senha do usuário ficará oculta. O valor padrão é **false**.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
