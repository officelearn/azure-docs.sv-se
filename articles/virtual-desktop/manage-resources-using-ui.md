---
title: Distribuera hanteringsverktyg med en Azure Resource Manager-mall - Azure
description: Så här installerar du ett användargränssnittsverktyg med en Azure Resource Manager-mall för att hantera Windows Virtual Desktop-resurser.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dac17da4aa3d3d73ccbbc101cba4326a17fec9b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127785"
---
# <a name="deploy-a-management-tool-with-an-azure-resource-manager-template"></a>Distribuera ett hanteringsverktyg med en Azure Resource Manager-mall

Instruktionerna i den här artikeln talar om för dig hur du distribuerar användargränssnittet med hjälp av en Azure Resource Manager-mall.

## <a name="important-considerations"></a>Att tänka på

Eftersom appen kräver samtycke till att interagera med Windows Virtual Desktop stöder det här verktyget inte B2B-scenarier (Business-to-Business). Varje Azure Active Directory -klient (AAD) -klientens prenumeration behöver en egen separat distribution av hanteringsverktyget.

Det här hanteringsverktyget är ett exempel. Microsoft kommer att tillhandahålla viktiga säkerhets- och kvalitetsuppdateringar. [Källkoden är tillgänglig i GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Kunder och partner uppmanas att anpassa verktyget så att det passar deras affärsbehov.

Så här följer du webbläsare som är kompatibla med hanteringsverktyget:
- Google Chrome 68 eller senare
- Microsoft Edge 40.15063 eller senare
- Mozilla Firefox 52.0 eller senare
- Safari 10 eller senare (endast macOS)

## <a name="what-you-need-to-deploy-the-management-tool"></a>Vad du behöver för att distribuera hanteringsverktyget

Innan du distribuerar hanteringsverktyget behöver du en Azure Active Directory-användare (Azure AD) för att skapa en appregistrering och distribuera hanteringsgränssnittet. Den här användaren måste:

- Ha Azure Multi-Factor Authentication (MFA) inaktiverat
- Ha behörighet att skapa resurser i din Azure-prenumeration
- Har behörighet att skapa ett Azure AD-program. Följ dessa steg för att kontrollera om användaren har de behörigheter som krävs genom att följa instruktionerna i [Obligatoriska behörigheter](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

När du har distribuerat och konfigurerat hanteringsverktyget rekommenderar vi att du ber en användare att starta hanteringsgränssnittet för att se till att allt fungerar. Användaren som startar hanteringsgränssnittet måste ha en rolltilldelning som gör att de kan visa eller redigera Windows Virtual Desktop-klienten.

## <a name="deploy-the-management-tool"></a>Distribuera hanteringsverktyget

Innan du börjar bör du se till att server- och klientapparna har medgivande genom att besöka [windows virtual desktop consent-sidan](https://rdweb.wvd.microsoft.com) för Azure Active Directory (AAD) som representeras.

Så här distribuerar du Mallen Azure Resource Management:

1. Gå till [sidan GitHub Azure RDS-mallar](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Distribuera mallen till Azure.
    - Om du distribuerar i en Enterprise-prenumeration bläddrar du nedåt och väljer **Distribuera till Azure**. 
    - Om du distribuerar i en cloud solution provider-prenumeration följer du dessa instruktioner för att distribuera till Azure:
        1. Bläddra nedåt och högerklicka på **Distribuera till Azure**och välj sedan Kopiera **länkplats**.
        2. Öppna en textredigerare som Anteckningar och klistra in länken där.
        3. Direkt <https://portal.azure.com/> efter och före hashtaggen (#) anger du ett at-tecken (@) följt av klientdomännamnet. Här är ett exempel på <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>formatet: .
        4. Logga in på Azure-portalen som användare med administratörs-/deltagarbehörighet till cloud solution provider-prenumerationen.
        5. Klistra in länken som du kopierade till textredigeraren i adressfältet.
3. Gör följande när du anger parametrarna:
    - För parametern **isServicePrincipal** väljer du **false**.
    - För autentiseringsuppgifterna anger du dina Azure AD-autentiseringsuppgifter med multifaktorautentisering inaktiverad. Dessa autentiseringsuppgifter används för att skapa Azure AD-programmet och Azure-resurser. Mer information finns i [det du behöver för att distribuera hanteringsverktyget](#what-you-need-to-deploy-the-management-tool).
    - För **applicationName**använder du ett unikt namn för din app som ska registreras i din Azure Active Directory. Det här namnet används också för webbapp-URL:en. Du kan till exempel använda ett namn som "Apr3UX".
4. När du har ange parametrarna godkänner du villkoren och väljer **Köp**.

## <a name="provide-consent-for-the-management-tool"></a>Ge samtycke till hanteringsverktyget

När GitHub Azure Resource Manager-mallen är klar hittar du en resursgrupp som innehåller två apptjänster tillsammans med en apptjänstplan i Azure-portalen.

Innan du loggar in och använder hanteringsverktyget måste du ge ditt samtycke till det nya Azure AD-programmet som är associerat med hanteringsverktyget. Med samtycke kan hanteringsverktyget ringa Windows Virtual Desktop-hanteringsanrop på uppdrag av den användare som för närvarande är inloggad på verktyget.

![En skärmbild som visar de behörigheter som ges när du samtycker till verktyget för hantering av användargränssnittet.](media/management-ui-delegated-permissions.png)

För att avgöra vilken användare du kan använda för att logga in på verktyget går du till [sidan med Azure Active Directory-användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) och noterar värdet för användare som kan **samtycka till att appar som får åtkomst till företagsdata för deras räkning**.

![En skärmdump som visar om användare kan bevilja samtycke till program för bara sin användare.](media/management-ui-user-consent-allowed.png)

- Om värdet är inställt på **Ja**kan du logga in med alla användarkonton i Azure Active Directory och ge endast den användaren tillstånd. Men om du loggar in på hanteringsverktyget med en annan användare senare måste du utföra samma medgivande igen.
- Om värdet är inställt på **Nej**måste du logga in som global administratör i Azure Active Directory och ge administratörsgodkännande för alla användare i katalogen. Inga andra användare kommer att möta en medgivande prompt.


När du har bestämt vilken användare du ska använda för att ge ditt samtycke följer du dessa instruktioner för att ge ditt samtycke till verktyget:

1. Gå till dina Azure-resurser, välj Azure App Services-resursen med namnet du angav i mallen (till exempel Apr3UX) och navigera till url:en som är associerad med den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med lämpligt Azure Active Directory-användarkonto.
3. Om du autentiserade med en global administratör kan du nu markera kryssrutan till **Samtycke för din organisations räkning**. Välj **Acceptera** om du vill ge medgivande.
   
   ![En skärmbild som visar hela samtyckessidan som användaren eller administratören kommer att se.](media/management-ui-consent-page.png)

Detta tar dig nu till hanteringsverktyget.

## <a name="use-the-management-tool"></a>Använda hanteringsverktyget

När du har gett ditt samtycke till organisationen eller för en viss användare kan du när som helst komma åt hanteringsverktyget.

Följ dessa instruktioner för att starta verktyget:

1. Välj Azure App Services-resursen med namnet du angav i mallen (till exempel Apr3UX) och navigera till url:en som är associerad med den. till exempel <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Logga in med autentiseringsuppgifterna för Windows Virtual Desktop.
3. När du uppmanas att välja en klientgrupp väljer du **Standardklientgrupp** i listrutan.
4. När du väljer **Standardklientgrupp**ska en meny visas till vänster i fönstret. På den här menyn hittar du namnet på din klientgrupp och väljer det.
  
  > [!NOTE]
  > Om du har en anpassad klientgrupp anger du namnet manuellt i stället för att välja från listrutan.

## <a name="report-issues"></a>Rapportera problem

Om du stöter på problem med hanteringsverktyget eller andra Windows Virtual Desktop-verktyg följer du anvisningarna i [Azure Resource Manager-mallar för Fjärrskrivbordstjänster för](https://github.com/Azure/RDS-Templates/blob/master/README.md) att rapportera dem på GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du distribuerar och ansluter till hanteringsverktyget kan du lära dig hur du använder Azure Service-hjälp för att övervaka serviceproblem och hälsorekommendationer. Mer information finns i [vår uppsättning tjänst varningar handledning](./set-up-service-alerts.md).