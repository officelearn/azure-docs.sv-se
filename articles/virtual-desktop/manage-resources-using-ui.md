---
title: Distribuera hanteringsverktyg – Azure
description: Så här installerar du ett användargränssnitt för att hantera resurser för virtuella Windows-skrivbordet.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: v-chjenk
ms.openlocfilehash: 9579db9836ef41706f2c6be09570fa7c1459e14f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620455"
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

- Det här är URL: en för RD broker: https:\//rdbroker.wvd.microsoft.com/
- Det här är resurs-URL: https:\//mrs-prod.ame.gbl/mrs-RDInfra-prod
- Använd dina AAD-autentiseringsuppgifter med MFA inaktiveras för att logga in på Azure. Se [vad du behöver att köra Azure Resource Manager-mallen](#what-you-need-to-run-the-azure-resource-manager-template).
- Använd ett unikt namn för det program som ska registreras i Azure Active Directory för hanteringsverktyg; till exempel Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Ge medgivande för hanteringsverktyget

När du har GitHub Azure Resource Manager mallen är klar, hittar du en resursgrupp som innehåller två apptjänster tillsammans med en app service-plan i Azure-portalen.

Innan du loggar in och använda hanteringsverktyget behöver du ge medgivande för det nya Azure Active Directory-programmet som är associerad med verktyget management. Genom att tillhandahålla medgivande, tillåter du att hanteringsverktyg att göra virtuellt skrivbord i Windows management-anrop för den användare som är inloggad i verktyget.

![En skärmbild som visar de behörigheter som tillhandahålls när samtycker du till UI-hanteringsverktyg.](media/management-ui-delegated-permissions.png)

För att avgöra vilken användare som du kan använda för att logga in på verktyget går du till din [inställningssidan för Azure Active Directory-användare](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) och anteckna värdet för **användare kan godkänna att appar får åtkomst till företagets data å deras vägnar** .

![En skärmbild som visar om användare kan ge medgivande till program för just deras användare.](media/management-ui-user-consent-allowed.png)

- Om värdet anges till **Ja**, du kan logga in med ett användarkonto i Azure Active Directory och ge medgivande för den användaren. Men om du loggar in till hanteringsverktyget med en annan användare senare, måste du utföra samma medgivande igen.
- Om värdet anges till **nr**, måste du logga in som Global administratör i Azure Active Directory och ange administratörens godkännande för alla användare i katalogen. Inga andra användare kommer att stöta på Kommandotolken medgivande.


Följ dessa instruktioner för att ge medgivande till verktyget när du har bestämt vilken användare som du använder för att ge medgivande:

1. Gå till dina Azure-resurser, Välj den Azure App Services-resursen med det namn du angav i mallen (till exempel Apr3UX) och gå till den URL som är associerade med den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med rätt Azure Active Directory-konto.
3. Om du autentiseras med en Global administratör, kan du nu välja kryssrutan för att **ge samtycke åt din organisation**. Välj **acceptera** att ge medgivande.
   
   ![En skärmbild som visar sidan fullständig medgivande att användaren eller administratören visas.](media/management-ui-consent-page.png)

Nu då kommer du till hanteringsverktyget.

## <a name="use-the-management-tool"></a>Använd hanteringsverktyget för

När du har angett ditt medgivande för organisationen eller för en viss användare, kan du komma åt verktyget management när som helst.

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
