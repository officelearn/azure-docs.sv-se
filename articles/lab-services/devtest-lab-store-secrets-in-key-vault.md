---
title: Store hemligheter i ett nyckelvalv i Azure DevTest Labs | Microsoft Docs
description: Lär dig mer om att lagra hemligheter i ett Azure Key Vault och använda dem när du skapar en virtuell dator med formeln, eller en miljö.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: d87c8a46459a9b4bf80bef895ec97e436d38e699
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186840"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Store-hemligheter i key vault i Azure DevTest Labs
Du kan behöva ange en komplex hemlighet när du använder Azure DevTest Labs: lösenordet för din Windows-VM, offentlig SSH-nyckel för Linux VM eller personlig åtkomsttoken att klona Git-lagringsplats via en artefakt. Hemligheter är vanligtvis långa och ha slumpmässiga tecken. Lägger till dem kan därför vara svårt och olämplig, särskilt om du använder samma hemlighet flera gånger.

För att lösa problemet och också hålla dina hemligheter på en säker plats, stöder DevTest Labs lagra hemligheter i ett [Azure-nyckelvalv](../key-vault/key-vault-overview.md). När en användare sparar en hemlighet för första gången, skapas automatiskt ett nyckelvalv i samma resursgrupp som innehåller labbet och lagrar hemligheten i nyckelvalvet DevTest Labs-tjänsten. DevTest Labs skapar ett separat nyckelvalv för varje användare. 

## <a name="save-a-secret-in-azure-key-vault"></a>Spara en hemlighet i Azure Key Vault
Om du vill spara din hemlighet i Azure Key Vault, gör du följande:

1. Välj **min hemligheter** på den vänstra menyn.
2. Ange en **namn** för hemligheten. Du ser det här namnet i den nedrullningsbara listan när du skapar en virtuell dator, formeln, eller en miljö. 
3. Ange hemlighet som den **värdet**.

    ![Store-hemlighet](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Använd en hemlighet från Azure Key Vault
När du vill ange en hemlighet för att skapa en virtuell dator, formeln, eller en miljö kan du antingen ange en hemlighet manuellt eller välj en sparad hemlighet från key vault. Om du vill använda en hemlighet som lagras i ditt nyckelvalv, gör du följande åtgärder:

1. Välj **använder en sparad hemlighet**. 
2. Välj din hemlighet från listrutan för **Välj en hemlighet**. 

    ![Använd hemlighet i virtuell dator](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Använd en hemlighet i en Azure Resource Manager-mall
Du kan ange ditt hemliga namn i en Azure Resource Manager-mall som används för att skapa en virtuell dator som du ser i följande exempel:

![Använd hemlighet i formeln eller](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa en virtuell dator med hjälp av hemligheten](devtest-lab-add-vm.md) 
- [Skapa en formel med hjälp av hemligheten](devtest-lab-manage-formulas.md)
- [Skapa en miljö med hjälp av hemligheten](devtest-lab-create-environment-from-arm.md)
