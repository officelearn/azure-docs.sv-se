---
title: Administration i Azure EA-portalen
description: I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 06/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: eefd4a9ddf2150728578a4e058686a3204e34930
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456563"
---
# <a name="azure-ea-portal-administration"></a>Administration i Azure EA-portalen

I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen (https://ea.azure.com). Azure EA-portalen är en onlinehanteringsportal där kunder kan hantera kostnaden för sina Azure EA-tjänster. Grundläggande information om Azure EA-portalen finns i artikeln [Kom igång med Azure EA-portalen](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

Företagsadministratörer kan associera befintliga konton till avdelningar under registreringen.

### <a name="to-associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj **Avdelning**.
1. Hovra över raden med kontot och välj pennikonen till höger.
1. Välj avdelning i den nedrullningsbara menyn.
1. Välj **Spara**.

## <a name="department-spending-quotas"></a>Avdelningens utgiftskvoter

EA-kunder kan ange eller ändra utgiftskvoter för varje avdelning under en registrering. Utgiftskvotens belopp anges för det aktuella åtagandeperioden. I slutet av den aktuella åtagandeperioden förlänger systemet den befintliga utgiftskvoten till nästa åtagandeperiod om inte värdena uppdateras.

Avdelningsadministratören kan visa utgiftskvoten utan bara företagsadministratören kan uppdatera kvotbeloppet. Företagsadministratören och avdelningsadministratören får meddelanden när kvoten har nått 50 %, 75 %, 90 % och 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Företagsadministratör för att ange kvoten:

 1. Öppna Azure EA-portalen.
 1. Välj **Hantera** i det vänstra navigeringsfältet.
 1. Välj fliken **Avdelning**.
 1. Välj avdelning.
 1. Välj pennsymbolen i avsnittet Avdelningsinformation eller välj symbolen **+ Lägg till avdelning** för att lägga till en utgiftskvot tillsammans med en ny avdelning.
 1. Under användningsinformationen anger du ett utgiftskvotbelopp i registreringens valuta i rutan Spending Quota $ (Utgiftskvot $) (måste vara större än 0).
    - Avdelningsnamnet och kostnadsstället kan också redigeras nu.
 1. Välj **Spara**.

Avdelningens utgiftskvot syns nu i vyn Department List (Avdelningslista) under fliken Department (Avdelning). I slutet av det aktuella åtagandet behåller Azure EA-portalen utgiftskvoterna till nästa åtagandeperiod.

Avdelningskvotens belopp är oberoende av det aktuella betalningsåtagandet, och kvotbeloppet och aviseringarna gäller bara för förstapartsanvändning. Avdelningens utgiftskvot är bara för information och tillämpar inte utgiftsgränser.

### <a name="department-administrator-to-view-the-quota"></a>Avdelningsadministratör för att visa kvoten:

1. Öppna Azure EA-portalen.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Klicka på fliken **Avdelning** och visa vyn Department List (Avdelningslista) med utgiftskvoter.

Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

Du kan administrera kostnader och användning för Azure EA i Azure EA-portalen. Det finns tre huvudsakliga roller i Azure EA-portalen:

- EA-administratör
- Avdelningsadministratör
- Kontoinnehavare

De olika rollerna har olika åtkomstnivå och behörighet.

Du kan läsa mer om användarroller under [Enterprise user roles](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles) (Roller för företagsanvändare).

## <a name="add-an-azure-ea-account"></a>Lägga till ett Azure EA-konto

Azure EA-kontot är en organisationsenhet i Azure EA-portalen. Den används för att administrera prenumerationer samt för rapportering. Om du ska kunna komma åt och använda Azure-tjänster måste du eller någon annan skapa ett konto åt dig.

Du kan läsa mer om Azure-konton under [Lägga till ett konto](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account).

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test-erbjudande

Som Azure Enterprise-administratör kan du aktivera kontoinnehavare i organisationen för att skapa prenumerationer baserade på EA Dev/Test-erbjudandet. För att göra det väljer du rutan Dev/Test för kontoägaren i Azure EA-portalen.

När du har markerat Dev/Test-rutan meddelar du kontoinnehavaren att han/hon kan ställa in de EA Dev/Test-prenumerationer som behövs för teamet med Dev/Test-prenumeranter.

Erbjudandet gör att aktiva Visual Studio-prenumeranter kan köra arbetsbelastningar för utveckling och testning i Azure till specialpris. Det ger åtkomst till det fullständiga galleriet med Dev/Test-avbildningar inklusive Windows 8.1 och Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Konfigurera Enterprise Dev/Test-erbjudandet:

1. Logga in som företagsadministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Konto**.
1. Välj raden för det konto där du vill aktivera Dev/Test-åtkomst.
1. Välj pennsymbolen till höger om raden.
1. Markera kryssrutan Dev/Test.
1. Välj **Spara**.

När en användare läggs till som kontoinnehavare via Azure EA-portalen konverteras alla Azure-prenumerationer associerade med kontoinnehavaren som baseras på antingen PAYG Dev/Test-erbjudandet eller erbjudanden med månatlig kredit för Visual Studio-prenumeranter till EA Dev/Test-erbjudandet. Prenumerationer baserade på andra erbjudandetyper, till exempel PAYG (betala per användning), associerade med kontoinnehavaren konverteras till Microsoft Azure Enterprise-erbjudanden.

Dev/Test-erbjudandet gäller för närvarande inte för Azure Gov-kunder.

## <a name="delete-subscription"></a>Ta bort prenumeration

Ta bort en prenumeration där du är kontoinnehavare:

1. Logga in på Azure-portalen med autentiseringsuppgifterna som är kopplade till ditt konto.
1. Välj **Prenumerationer** på navmenyn.
1. På prenumerationsfliken i det övre vänstra hörnet på sidan väljer du den prenumeration du vill ha för att avbryta och välj **Avbryt prenumeration** för att starta fliken Avbryt.
1. Ange prenumerationens namn och välj en uppsägningsorsak och väljer knappen **Avbryt prenumeration**.

Bara kontoadministratörer kan avbryta prenumerationer.

Mer information finns i [Vad händer när jag har sagt upp min prenumeration?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription).

## <a name="delete-an-account"></a>Ta bort ett konto

Borttagning av konto kan bara slutföras för aktiva konton utan aktiva prenumerationer.

1. I EA-portalen väljer du **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Konto**.
1. I tabellen med konton väljer du de konton du vill ta bort.
1. Välj X-symbolen till höger om kontoraden.
1. När det inte finns några aktiva prenumerationer under kontot väljer du knappen **Ja** under kontoraden för att bekräfta borttagningen av kontot.

## <a name="update-notification-settings"></a>Uppdatera meddelandeinställningar

Företagsadministratörer registreras automatiskt för att få användningsmeddelanden kopplade till sin registrering. Varje företagsadministratör kan ändra hur ofta meddelanden ska skickas, eller så kan de stängas av helt.

Du kan se kontaktuppgifterna för meddelanden i avsnittet **Meddelandekontakt** i Azure EA-portalen. Genom att hantera dina meddelandekontakter ser du till att rätt personer i organisationen får meddelanden om Azure EA.

Så här visar du aktuella meddelandeinställningar:

1. Öppna Azure EA-portalen och navigera till **Hantera** > **Meddelandekontakt**.
2. E-postadress – e-postadressen som är kopplad till företagsadministratörens Microsoft-konto, arbetskonto eller skolkonto som tar emot meddelanden.
3. Frekvens för meddelanden om utestående saldo – hur ofta meddelanden skickas till respektive företagsadministratör.

Så här lägger du till en kontakt:

1. Välj **+ Lägg till kontakt**.
2. Ange e-postadressen och bekräfta den.
3. Välj **Spara**.

Den nya meddelandekontakten visas i avsnittet **Meddelandekontakt**. Om du vill ändra meddelandefrekvensen markerar du meddelandekontakten och väljer pennsymbolen till höger om den markerade raden. Ställ in frekvensen som **varje dag**, **varje vecka**, **varje månad** eller **aldrig**.

Du kan välja att hoppa över livscykelmeddelandena _täckningsperiodens slutdatum närmar sig_ och _datumet för inaktivering och avetablering närmar sig_. Om du hoppar över livscykelmeddelandena skickas inga meddelanden om slutdatumet för täckningsperioden eller avtalet.

## <a name="azure-sponsorship-offer"></a>Azure-sponsringserbjudande

Azure-sponsringserbjudandet är ett begränsat, sponsrat Microsoft Azure-konto. Inbjudan skickas via e-post till ett begränsat antal kunder som väljs ut av Microsoft. Om du är berättigad till Microsoft Azure-sponsringserbjudandet så har du fått en e-postinbjudan till ditt konto-id.

Om du vill ha mer information kan du skapa en [supportbegäran för sponsringsaktivering](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Konvertering till autentisering med arbets- eller skolkonto

Azure Enterprise-användare kan konvertera autentiseringstypen från ett Microsoft-konto (MSA eller Live ID) till ett arbets- eller skolkonto (som använder Active Directory i Azure).

Så här börjar du:

1. Lägg till arbets- eller skolkontot i Azure EA-portalen i de rollers som krävs.
1. Om du får felmeddelanden kanske kontot inte är giltigt i den aktiva katalogen.  Azure använder användarhuvudnamn (UPN), som inte alltid är identiskt med e-postadressen.
1. Autentisera till Azure EA-portalen med arbets- eller skolkonto.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Så här konverterar du prenumerationer från Microsoft-konton för arbets- eller skolkonton:

1. Logga in på hanteringsportalen med det Microsoft-konto som äger prenumerationerna.
1. Använd överföring av kontoägarskap för att flytta till det nya kontot.
1. Nu bör Microsoft-kontot vara fritt från aktiva prenumerationer och kan tas bort.
1. Borttagna konton blir kvar i vyn i portalen med inaktiv status av historiska faktureringsskäl.  Du kan filtrera bort dem från vyn genom att markera kryssrutan för att bara visa aktiva konton.

## <a name="account-subscription-ownership-faq"></a>Vanliga frågor och svar om prenumerationsägarskap

I det här dokumentet besvaras frågor som rör ägarskapet för kontoprenumerationen.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hur många Azure-kontoinnehavare kan du ha per prenumeration?

Bara en kontoinnehavare tillåts per prenumeration.  Ytterligare roller kan läggas till med rollbaserad åtkomst eller Åtkomstkontroll (IAM) på prenumerationsfliken i det övre vänstra hörnet på sidan på [portal.azure.com]](https://portal.azure.com).

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan en Azure-kontoinnehavare listas under mer än en avdelning?

Nej, en kontoinnehavare kan bara associeras med en enda avdelning. Principen är till för att säkerställa korrekt övervakning och fördelning av kostnader och utgifter kopplade till avdelningen de är justerade mot under EA-registreringen i Azure EA-portalen.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan en Azure-kontoinnehavare listas som en säkerhetsgrupp?

Nej, en prenumerationsägare måste vara ett unikt Microsoft-konto (MSA) eller en Azure Active Directory-autentisering (AAD). För att ta hänsyn till följden i organisationen kan du skapa allmänna konton och använda AAD för att hantera prenumerationsåtkomst.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan en enskild användare äga flera prenumerationer?

En Azure-kontoinnehavare kan skapa och hantera ett obegränsat antal prenumerationer.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hur kan jag få åtkoms till/visa alla min organisations prenumerationer?

Idag måste detta göras med en princip. Det betyder att du skulle kräva att ditt konto läggs till i en prenumerationsroll med rollbaserad åtkomst för varje prenumeration som skapas.

### <a name="where-do-i-go-to-create-a-subscription"></a>Var skapar jag en prenumeration?

Innan du kan skapa en Azure EA-prenumeration måste ditt konto läggas till i rollen som kontoinnehavare av din administratören för din EA-registrering i Azure EA-portalen. Du måste sedan logga in på Azure EA-portalen för att få din rättighet att skapa prenumerationer för EA-erbjudanden. Vi rekommenderar att din första EA-prenumeration skapas via länken ”+ Add Subscription” (+ Lägg till prenumeration) på prenumerationsfliken i EA-portalen.  Men när ditt konto är berättigat kan det vara enklare att skapa prenumerationer i portal.azure.com på prenumerationsfliken i det övre högra hörnet på sidan, där du både kan skapa och byta namn på prenumerationen i ett enda steg.

### <a name="who-can-create-a-subscription"></a>Vem kan skapa en prenumeration?

Om du vill skapa en prenumeration för ett Azure-erbjudande för företag måste du vara berättigad i rollen som kontoinnehavare i [EA-portalen](https://ea.azure.com).

## <a name="next-steps"></a>Nästa steg

- Läs om hur du kan spara pengar med [reservationer för virtuella datorer](ea-portal-vm-reservations.md).
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](ea-portal-troubleshoot.md).
