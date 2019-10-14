---
title: Distribuera hanterings verktyget – Azure
description: Så här installerar du ett användar gränssnitts verktyg för att hantera Windows-resurser för virtuella skriv bord.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: 175c5173091bee85a5c9fc4a50e9df5d3f73da13
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300912"
---
# <a name="tutorial-deploy-a-management-tool"></a>Självstudie: Distribuera ett hanterings verktyg

Hanterings verktyget tillhandahåller ett användar gränssnitt för hantering av Microsoft-resurser för virtuella skriv bord. I den här självstudien får du lära dig hur du distribuerar och ansluter till hanterings verktyget.

>[!NOTE]
>De här anvisningarna gäller för en Windows-konfiguration med en virtuell dator som kan användas med din organisations befintliga processer.

## <a name="important-considerations"></a>Viktiga överväganden

Eftersom appen kräver medgivande för att interagera med Windows Virtual Desktop, stöder det här verktyget inte Business-to-Business-scenarier (B2B). Varje Azure Active Directory (AAD)-klient organisations prenumeration måste ha en egen separat distribution av hanterings verktyget.

Det här hanterings verktyget är ett exempel. Microsoft kommer att tillhandahålla viktiga säkerhets-och kvalitets uppdateringar. [Käll koden finns i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Kunder och partner uppmanas att anpassa verktyget så att det passar deras affärs behov.

Följande webbläsare är kompatibla med hanterings verktyget:
- Google Chrome 68 eller senare
- Microsoft Edge 40,15063 eller senare
- Mozilla Firefox 52,0 eller senare
- Safari 10 eller senare (endast macOS)

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver för att köra Azure Resource Manager-mallen

Innan du distribuerar Azure Resource Manager-mallen behöver du en Azure Active Directory användare för att distribuera hanterings gränssnittet. Den här användaren måste:

- Ha Azure Multi-Factor Authentication (MFA) inaktiverat
- Har behörighet att skapa resurser i din Azure-prenumeration
- Har behörighet att skapa ett Azure AD-program. Följ de här stegen för att kontrol lera om användaren har de [behörigheter som krävs](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

När du har distribuerat Azure Resource Manager-mallen vill du starta hanterings gränssnittet för att verifiera. Den här användaren måste:
- Ha en roll tilldelning för att visa eller redigera din Windows-klient för virtuella skriv bord

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Kör Azure Resource Manager-mallen för att etablera hanterings gränssnittet

Innan du börjar bör du kontrol lera att servern och klientens appar har medgivande genom att besöka [sidan för godkännande av Windows-dator](https://rdweb.wvd.microsoft.com) för den Azure Active Directory (AAD) som visas.

Följ de här anvisningarna för att distribuera Azure-resurs hanterings mal len:

1. Gå till [sidan GitHub Azure RDS – templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuera mallen till Azure.
    - Om du distribuerar i en Enterprise-prenumeration kan du rulla nedåt och välja **distribuera till Azure**. Se [rikt linjer för mallparametrar](#guidance-for-template-parameters).
    - Om du distribuerar i en prenumeration på Cloud Solution Provider följer du de här anvisningarna för att distribuera till Azure:
        1. Rulla nedåt och högerklicka på **distribuera till Azure**och välj sedan **Kopiera länk plats**.
        2. Öppna en text redigerare som anteckningar och klistra in länken där.
        3. Höger efter <https://portal.azure.com/> och innan hashtagg (#) anger ett @-tecken följt av klient domän namnet. Här är ett exempel på formatet: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Logga in på Azure Portal som en användare med administratörs-/deltagar behörighet för Cloud Solution Provider-prenumerationen.
        5. Klistra in länken som du kopierade till text redigeraren i adress fältet.

### <a name="guidance-for-template-parameters"></a>Vägledning för mallparametrar
Så här anger du parametrar för att konfigurera verktyget:

- Detta är URL: en för RD Broker: https: \//rdbroker. WVD. Microsoft. com/
- Detta är resurs-URL: en: https: \//Mrs-Prod. amn. GBL/fru-RDInfra-Prod
- Använd dina AAD-autentiseringsuppgifter med MFA inaktiverat för att logga in på Azure. Se [vad du behöver för att köra Azure Resource Manager-mallen](#what-you-need-to-run-the-azure-resource-manager-template).
- Använd ett unikt namn för programmet som ska registreras i Azure Active Directory för hanterings verktyget. till exempel Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Tillhandahåll medgivande för hanterings verktyget

När GitHub Azure Resource Manager-mallen har slutförts hittar du en resurs grupp som innehåller två app Services tillsammans med en app service-plan i Azure Portal.

Innan du loggar in och använder hanterings verktyget måste du ange ett medgivande för det nya Azure Active Directorys programmet som är associerat med hanterings verktyget. Genom att tillhandahålla medgivande kan du låta hanterings verktyget göra Windows-hantering för virtuella skriv bord åt den användare som är inloggad i verktyget.

![En skärm bild som visar de behörigheter som tillhandahålls när du godkänner användar gränssnitts hanterings verktyget.](media/management-ui-delegated-permissions.png)

För att avgöra vilken användare du kan använda för att logga in på verktyget går du till [sidan Azure Active Directory användar inställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) och noterar värdet för **användarna kan godkänna att appar får åtkomst till företags data för deras räkning**.

![En skärm bild som visar om användarna kan ge tillåtelse till program enbart för användare.](media/management-ui-user-consent-allowed.png)

- Om värdet är inställt på **Ja**kan du logga in med ett användar konto i Azure Active Directory och bara ge användaren tillåtelse. Men om du loggar in på hanterings verktyget med en annan användare senare, måste du utföra samma medgivande igen.
- Om värdet är inställt på **Nej**, måste du logga in som global administratör i Azure Active Directory och ge administratörs tillåtelse för alla användare i katalogen. Inga andra användare får ett medgivande.


När du har bestämt vilken användare du ska använda för att ge sitt medgivande följer du de här anvisningarna för att ge ditt medgivande till verktyget:

1. Gå till dina Azure-resurser, Välj resursen för Azure App tjänster med det namn som du angav i mallen (till exempel Apr3UX) och navigera till den URL som är kopplad till den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med lämpligt Azure Active Directory användar konto.
3. Om du har autentiserats med en global administratör kan du nu Markera kryss rutan för **din organisations räkning**. Välj **acceptera** för att ge medgivande.
   
   ![En skärm bild som visar sidan för fullständigt godkännande som användaren eller administratören ser.](media/management-ui-consent-page.png)

Nu ska du gå till hanterings verktyget.

## <a name="use-the-management-tool"></a>Använd hanterings verktyget

När du har beviljat godkännande för organisationen eller för en viss användare kan du när som helst komma åt hanterings verktyget.

Följ dessa instruktioner för att starta verktyget:

1. Välj resursen Azure App tjänster med det namn som du angav i mallen (till exempel Apr3UX) och navigera till den URL som är kopplad till den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med dina Windows-autentiseringsuppgifter för virtuella skriv bord.
3. När du uppmanas att välja en klient grupp väljer du **standard grupp för klient organisation** i list rutan.

> [!NOTE]
> Om du har en anpassad klient grupp anger du namnet manuellt i stället för att välja i list rutan.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluter till hanterings verktyget kan du lära dig hur du använder Azure Service Health för att övervaka tjänst problem och hälso rekommendationer.

> [!div class="nextstepaction"]
> [Själv studie kurs om att konfigurera service varningar](./set-up-service-alerts.md)
