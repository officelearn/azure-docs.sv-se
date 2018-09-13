---
title: Hantera Nyckelvalv i Azure Stack med hjälp av portalen | Microsoft Docs
description: Lär dig att hantera Nyckelvalv i Azure Stack med hjälp av portalen
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: D4300668-461F-45F6-BF3B-33B502C39D17
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2018
ms.author: sethm
ms.openlocfilehash: 51c04a567ff953c4e84930e3feae448f78627683
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713943"
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Hantera Nyckelvalv i Azure Stack med hjälp av portalen

Du kan hantera Nyckelvalv i Azure Stack med hjälp av Azure Stack-portalen. Den här artikeln hjälper dig att komma igång med att skapa och hantera ett nyckelvalv i Azure Stack.

## <a name="prerequisites"></a>Förutsättningar

Du måste prenumerera på ett erbjudande som innehåller Azure Key Vault-tjänsten.

## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).

2. Från instrumentpanelen väljer **+ skapa en resurs** > **säkerhet + identitet** > **Key Vault**.

    ![Key Vault-skärmen](media/azure-stack-kv-manage-portal/image1.png)

3. I den **skapa Nyckelvalv** fönstret tilldela en **namn** för ditt valv. Vault-namn får bara innehålla alfanumeriska tecken och specialtecken bindestreck (-). De får inte börja med en siffra.

4. Välj en **prenumeration** från listan över tillgängliga prenumerationer. Alla prenumerationer som erbjuder Key Vault-tjänsten visas i den nedrullningsbara listan.

5. Välj en befintlig **resursgrupp** eller skapa en ny.

6. Välj den **prisnivå**.
    >[!NOTE]
    > Viktiga valv i Azure Stack Development Kit support **Standard** endast SKU: er.

7. Välj något av de befintliga **åtkomstprinciper** eller skapa en ny. En åtkomstprincip kan du bevilja behörigheter för en användare, program eller en säkerhetsgrupp att utföra åtgärder med det här valvet.

8. Dessutom kan du välja en **avancerade åtkomstprincip** att aktivera åtkomst till funktioner. Till exempel: virtuella datorer (VM) för distributionen, Resource Manager för malldistributionen och åtkomst till Azure Disk Encryption för volymkryptering.

9. När du har konfigurerat inställningarna, Välj **OK**, och välj sedan **skapa**. Nu startar distributionen nyckelvalv.

## <a name="manage-keys-and-secrets"></a>Hantera nycklar och hemligheter

När du har skapat ett valv kan du använda följande steg för att skapa och hantera nycklar och hemligheter i valvet.

### <a name="create-a-key"></a>Skapa en nyckel

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).

2. Från instrumentpanelen väljer **alla resurser**, Välj det nyckelvalv som du skapade tidigare och välj sedan den **nycklar** panelen.

3. I den **nycklar** väljer **Lägg till**.

4. I den **skapar du en nyckel** rutan i listan över **alternativ**, välja den metod som du vill använda för att skapa en nyckel. Du kan **generera** en ny nyckel **överför** en befintlig nyckel eller använda **återställa säkerhetskopierade** att välja en säkerhetskopia av en nyckel.

5. Ange en **namn** för din nyckel. Nyckelnamnet kan innehålla endast alfanumeriska tecken och specialtecken bindestreck (-).

6. Alternativt kan du konfigurera den **ange Aktiveringsdatum** och **ange förfallodatum** värden för din nyckel.

7. Välj **skapa** att starta distributionen.

När nyckeln har skapats, kan du välja den under **nycklar** och visa eller ändra dess egenskaper. Egenskapsavsnittet innehåller den **nyckelidentifierare**, vilket är en identifierare URI (Uniform Resource) som externa program använder för att få åtkomst till den här nyckeln. För att begränsa åtgärder på den här nyckeln, konfigurerar du inställningarna under **tillåtna åtgärder**.

![URI-nyckel](media/azure-stack-kv-manage-portal/image4.png)

### <a name="create-a-secret"></a>Skapa en hemlighet

1. Logga in på den [användarportalen](https://portal.local.azurestack.external).
2. Från instrumentpanelen väljer **alla resurser**, Välj det nyckelvalv som du skapade tidigare och välj sedan den **hemligheter** panelen.

3. Under **hemligheter**väljer **Lägg till**.

4. Under **skapa en hemlighet**, i listan över **Uppladdningsalternativ**, Välj ett alternativ som du vill skapa en hemlighet. Du kan skapa en hemlighet **manuellt** om du anger ett värde för hemlighet eller ladda upp en **certifikat** från din lokala dator.

5. Ange en **namn** för hemligheten. Det hemliga namnet kan innehålla endast alfanumeriska tecken och specialtecken bindestreck (-).

6. Du kan också ange den **innehållstyp**, och konfigurera värden för **ange Aktiveringsdatum** och **ange förfallodatum** för hemligheten.

7. Välj **skapa** att starta distributionen.

När hemligheten har skapats, kan du välja den under **hemligheter** och visa eller ändra dess egenskaper. Den **hemlig identifierare** är en URI som externa program kan använda för att få åtkomst till den här hemligheten.

![URI-hemlighet](media/azure-stack-kv-manage-portal/image5.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera en virtuell dator genom att hämta lösenord som lagras i Key Vault](azure-stack-kv-deploy-vm-with-secret.md)
* [Distribuera en virtuell dator med certifikat som lagras i Key Vault](azure-stack-kv-push-secret-into-vm.md)
