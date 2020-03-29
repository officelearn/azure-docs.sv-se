---
title: Lagra hemligheter i ett nyckelvalv i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lagrar hemligheter i ett Azure Key Vault och använder dem när du skapar en virtuell dator, formel eller en miljö.
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
ms.openlocfilehash: 29a4d20390575778ccdecde390c257ccf6a48eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720936"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Lagra hemligheter i ett nyckelvalv i Azure DevTest Labs
Du kan behöva ange en komplex hemlighet när du använder Azure DevTest Labs: lösenord för din Windows VM, offentlig SSH-nyckel för din Virtuella Linux-dator eller personlig åtkomsttoken för att klona din Git-repa via en artefakt. Hemligheter är oftast långa och har slumpmässiga tecken. Därför kan det vara svårt och obekvämt att ange dem, särskilt om du använder samma hemliga flera gånger.

För att lösa detta problem och även hålla dina hemligheter på ett säkert ställe, devtest labs stöder lagring av hemligheter i en [Azure nyckel valv](../key-vault/key-vault-overview.md). När en användare sparar en hemlighet för första gången skapar devtest labs-tjänsten automatiskt ett nyckelvalv i samma resursgrupp som innehåller labbet och lagrar hemligheten i nyckelvalvet. DevTest Labs skapar ett separat nyckelvalv för varje användare. 

Observera att labbanvändaren först måste skapa en virtuell labbdator innan de kan skapa en hemlighet i nyckelvalvet. Detta beror på att DevTest Lab-tjänsten måste associera labbanvändaren med ett giltigt användardokument innan de tillåts skapa och lagra hemligheter i nyckelvalvet. 


## <a name="save-a-secret-in-azure-key-vault"></a>Spara en hemlighet i Azure Key Vault
Så här sparar du hemligheten i Azure Key Vault:

1. Välj **Mina hemligheter** på den vänstra menyn.
2. Ange ett **namn** på hemligheten. Du ser det här namnet i listrutan när du skapar en virtuell dator, formel eller en miljö. 
3. Ange hemligheten som **värde**.

    ![Lagra hemlighet](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Använda en hemlighet från Azure Key Vault
När du behöver ange en hemlighet för att skapa en virtuell dator, formel eller en miljö kan du antingen ange en hemlighet manuellt eller välja en sparad hemlighet från nyckelvalvet. Så här använder du en hemlighet som lagras i nyckelvalvet:

1. Välj **Använd en sparad hemlighet**. 
2. Välj din hemlighet i listrutan för **Välj en hemlighet**. 

    ![Använd hemlighet i virtuell dator](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Använda en hemlighet i en Azure Resource Manager-mall
Du kan ange ditt hemliga namn i en Azure Resource Manager-mall som används för att skapa en virtuell dator som visas i följande exempel:

![Använd hemlighet i formel eller miljö](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Nästa steg

- [Skapa en virtuell dator med hemligheten](devtest-lab-add-vm.md) 
- [Skapa en formel med hemligheten](devtest-lab-manage-formulas.md)
- [Skapa en miljö med hjälp av hemligheten](devtest-lab-create-environment-from-arm.md)
