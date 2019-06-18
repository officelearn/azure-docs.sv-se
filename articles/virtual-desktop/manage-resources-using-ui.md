---
title: Distribuera hanteringsverktyg – Azure
description: Så här installerar du ett användargränssnitt för att hantera resurser för virtuella Windows-skrivbordet.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 3635cd422e4c7a064d3317401b734f3bc6ccb6c6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67136451"
---
# <a name="tutorial-deploy-a-management-tool"></a>Självstudier: Distribuera ett hanteringsverktyg

Verktyget management innehåller ett användargränssnitt (UI) för att hantera Microsoft Virtual Desktop Preview resurser. I de här självstudierna lär du dig att skapa och ansluta till hanteringsverktyget.

>[!NOTE]
>Dessa instruktioner är för en virtuell Windows-skrivbordet förhandsversion konfiguration som kan användas med din organisations befintliga processer.

## <a name="important-considerations"></a>Att tänka på

Eftersom appen kräver godkännande för att interagera med virtuella Windows-skrivbordet, det här verktyget stöder inte Business-to-Business (B2B)-scenarier. Varje Azure Active Directory (AAD)-innehavarens prenumeration behöver en egen separat distributionen av management-verktyget.

Den här hanteringsverktyg är ett exempel. Microsoft meddelar viktiga säkerhets- och kvalitetsuppdateringar. Den [källkoden finns i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Kunder och partner uppmuntras att anpassa verktyget för att anpassa sina affärsbehov.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver att köra Azure Resource Manager-mall

Innan du distribuerar Azure Resource Manager-mall, måste en Azure Active Directory-användare att distribuera management UI. Den här användaren måste:

- Har Azure Multi-Factor Authentication (MFA) är inaktiverad
- Behörighet att skapa resurser i din Azure-prenumeration
- Behörighet att skapa ett Azure AD-program. Följ stegen nedan för att kontrollera om användaren har den [behörigheter som krävs för](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

När du har distribuerat Azure Resource Manager-mallen, bör du starta management användargränssnitt för att verifiera. Den här användaren måste:
- Har någon rolltilldelning för att visa eller redigera din virtuella skrivbordet i Windows-klient

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Kör Azure Resource Manager-mall för att etablera management UI

Innan du börjar, kontrollera server- och -appar har medgivande genom att besöka den [Windows Virtual Desktop godkänna sidan](https://rdweb.wvd.microsoft.com) för den Azure Active Directory (AAD) visas.

Följ dessa instruktioner för att distribuera Azure Resource Manager-mallen:

1. Gå till den [GitHub RDS-mallar för Azure-sidan](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuera mallen till Azure.
    - Om du distribuerar i en prenumeration på Enterprise, rulla nedåt och välj **distribuera till Azure**. Se [vägledning för mallparametrarna](#guidance-for-template-parameters).
    - Om du distribuerar i en Cloud Solution Provider-prenumeration, följer du dessa instruktioner för att distribuera till Azure:
        1. Rulla nedåt och högerklicka på **distribuera till Azure**och välj sedan **kopia länkplats**.
        2. Öppna en textredigerare, till exempel Anteckningar och klistra in det på länken.
        3. Direkt efter <https://portal.azure.com/> och innan hashtaggen (#), ange ett snabel-a (@) följt av klientens domännamn. Här är ett exempel på format: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Logga in på Azure Portal som en användare med behörigheter för administratör/deltagare i Cloud Solution Provider-prenumerationen.
        5. Klistra in länken som du kopierade till textredigeraren i adressfältet.

### <a name="guidance-for-template-parameters"></a>Vägledning för mallparametrar
Nedan visas hur du ange parametrar för att konfigurera verktyget:

- Det här är URL: en för RD broker:  <https://rdbroker.wvd.microsoft.com/>
- Det här är resurs-URL:  <https://mrs-prod.ame.gbl/mrs-RDInfra-prod>
- Använd dina AAD-autentiseringsuppgifter med MFA inaktiveras för att logga in på Azure. Se [vad du behöver att köra Azure Resource Manager-mallen](#what-you-need-to-run-the-azure-resource-manager-template).
- Använd ett unikt namn för det program som ska registreras i Azure Active Directory för hanteringsverktyg; till exempel Apr3UX.

## <a name="use-the-management-tool"></a>Använd hanteringsverktyget för

När du har GitHub Azure Resource Manager mallen är klar, hittar du en resursgrupp som innehåller två apptjänster tillsammans med en app service-plan i Azure-portalen.

Följ dessa instruktioner för att starta verktyget:

1. Välj den Azure App Services-resursen med det namn du angav i mallen (till exempel Apr3UX) och gå till den URL som är associerade med den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med dina autentiseringsuppgifter för virtuella Windows-skrivbordet.
3. När du uppmanas att välja en klient-grupp, Välj **standard klient grupp** från den nedrullningsbara listan.

> [!NOTE]
> Om du har en anpassad klient-grupp anger du namnet manuellt i stället för att välja från den nedrullningsbara listan.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluta till hanteringsverktyget, kan du lära dig hur du använder Azure Service Health övervakar karaktär och hälsoråd.

> [!div class="nextstepaction"]
> [Konfigurera självstudie för service-aviseringar](./set-up-service-alerts.md)
