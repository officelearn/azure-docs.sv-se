---
title: "Azure Authenticator för Android | Microsoft Docs"
description: "Microsoft Azure Authenticator-appen kan användas för att logga in till arbetsresurser. Azure Authenticator-appen meddelar dig om en väntande tvåfaktors verifieringsbegäran genom att visa en avisering till din mobila enhet."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 60a5cc2ecc550c76ca3cb1f1d4d20070b3e3b035
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="azure-authenticator-for-android"></a>Azure Authenticator för Android
IT-administratören kanske har rekommenderat dig använda Microsoft Azure Authenticator för att logga in till dina arbetsresurser. Det här programmet innehåller de här två alternativen inloggning:

* Multifaktorautentisering kan du skydda dina arbets- eller skolkonto konton med tvåstegsverifiering. Du loggar in med något du känner till (till exempel lösenord) och skyddar kontot ytterligare med något du har (en säkerhetsnyckel från den här appen). Azure Authenticator-appen meddelar dig om en väntande tvåfaktors verifieringsbegäran genom att visa en avisering till din mobila enhet. Du behöver bara visa förfrågan i appen och tryck verifiera eller Avbryt. Alternativt kan du uppmanas att ange lösenordet som visas i appen.
* Arbetskonto kan du göra din Android-telefon eller surfplatta till en betrodd enhet och tillhandahålla enkel inloggning (SSO) för företagets program. IT-administratören kan kräva att du kan lägga till ett arbetskonto för att komma åt företagets resurser. Enkel inloggning kan du logga in en gång och automatiskt få tillgång till logga in i alla program som företaget har gjorts tillgängliga för dig.

## <a name="installing-the-azure-authenticator-app"></a>Installera Azure Authenticator-appen
Du kan installera Azure Authenticator-appen från Google Play-butiken.
Instruktionerna för att lägga till arbetskonto från Samsung Android-enhet jämfört med en Samsung Android-enhet är något annorlunda. Instruktioner för både visas nedan.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Lägga till arbetskontot från Samsung Android-enhet
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Lägga till arbetskonto via appen startsidan
Följande instruktioner gäller Samsung GS3 och ovan telefoner eller meddelande 2 och högre surfplattor.

1. På startsidan för appen, accepterar du licensavtalet (EULA).
2. På skärmen aktivera konto klickar du på snabbmenyn till höger och välj **Arbetskonto**.
3. Lägg till konto skärmen Välj ** arbete konto **.
4. Klicka på Aktivera enheten administratör skärmen **aktivera**.
5. Markerar du kryssrutan och klickar på skärmen sekretesspolicy **Bekräfta**.
6. Ange användar-ID som tillhandahålls av din organisation och klicka på skärmen Anslut till arbetsplatsen **ansluta**.
7. Om du vill logga in på Azure Authenticator-appen, ange din organisations en *** onto och lösenordet och klickar på **logga in**.
8. På nästa skärm som visar information om multifaktorautentisering (MFA) för läggs säkerhet och är valfritt. Den här skärmen visas om ditt arbete eller skola kräver andra faktor-autentisering för att skapa arbetskonto. Den innehåller instruktioner för att verifiera ditt konto ytterligare.
9. Anslut till arbetsplatsen skärmen visar meddelandet ”**anslutning till din arbetsplats**”. Azure authenticator-appen försöker ansluta enheten till arbetsplatsen.
10. Du bör se meddelandet Arbetsplatsanslutna på nästa skärm.

> [!NOTE]
> Du får ett enda arbetskonto på enheten.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Att lägga till arbetskontot från inställningsmenyn
När du har installerat Azure Authenticator-appen, kan du också skapa ett arbetskonto från Android Kontoansvariga.

1. Från menyn Inställningar navigerar du till **konton** och på **Lägg till konto**.
2. Följ steg 3 – 10 i proceduren med att lägga till arbetskonto via appen startsidan, att lägga till ett arbetskonto.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Att lägga till arbetskontot från en Samsung Android-enhet
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Lägga till arbetskonto via appen startsidan
1. På startsidan för appen, accepterar du licensavtalet (EULA).
2. På skärmen aktivera konto klickar du på snabbmenyn till höger och välj **Arbetskonto**.
3. Klicka på skärmen konton **Lägg till konto**.
4. Om du ser skärmen konton, klickar du på **Lägg till konto**. Om ett arbetskonto redan har skapats tidigare, visas en Sync-skärm som visar den befintliga arbetskonto. Du kan behålla arbetskontot genom att bara trycka på pilen tillbaka till på startsidan. Alternativt kan du välja kontot som ska ta bort och återskapa en ny arbete konto på den till arbetsplatsen skärm, ange användar-ID som tillhandahålls av din organisation och klickar du på Anslut.
5. Om du vill logga in på Azure Authenticator-appen, ange ditt organisationskonto och lösenord och klicka på **logga in**.
6. På nästa skärm som visar information om multifaktorautentisering (MFA) för läggs säkerhet och är valfritt. Den här skärmen visas om ditt arbete eller skola kräver andra faktor-autentisering för att skapa arbetskonto. Den innehåller instruktioner för att verifiera ditt konto ytterligare.
7. Klicka på **OK** på nästa skärm. Ändra inte certifikatets namn.
   meddelandet ”ansluta till din arbetsplats”. Azure authenticator-appen försöker ansluta enheten till arbetsplatsen.
   Du bör se meddelandet Arbetsplatsanslutna på nästa skärm.

> [!NOTE]
> Du får ett enda arbetskonto på enheten.
> 
> 

När du har installerat Azure Authenticator-appen, kan du också skapa ett arbetskonto från Android Kontoansvariga.

1. Från den **inställningar** menyn navigerar du till konton och på **Lägg till konto**.
2. Följ steg 2-7 i proceduren med att lägga till arbetskonto via den app hem skärmen **, om du vill lägga till ett arbetskonto.

### <a name="how-to-find-out-which-version-is-installed"></a>Ta reda på vilken version är installerad
1. Du kan kontrollera vilken version av Azure Authenticator-appen och associerade tjänstversioner är installerade på din enhet.
2. I popup-menyn, klickar du på **om**.
3. Skärmen om visar tjänster på appen och versioner som är installerad på din enhet.

### <a name="sending-log-files-to-report-issues"></a>Skicka loggfiler och rapportera problem
1. Följ anvisningarna på Microsoft-supporten att rapportera en incident med Azure Authenticator-appen, hämta incidentnummer och skicka loggfiler mot tilldelad incident numret på följande sätt:
2. I popup-menyn, klickar du på **loggning**.
3. Om du har en öppen incident Microsoft Support kan anteckna incident numret (du behöver den för senare). Om du inte redan har skapat en supportincident och vill ha hjälp, följer du anvisningarna på [Microsoft-supporten](https://support.microsoft.com/en-us/contactus) att öppna en ny incident.
4. Klicka på skärmen loggning **Skicka nu**.
5. Välj den e-provider som du vill använda.
6. Om du redan har en öppen incident Microsoft Support kontakta supportteknikern tilldelats problemet att ta reda på hur du skickar loggdata och associera den med en incident. Supportteknikern ger dig information om e-postadress och ämne rad. Om du inte redan har en supportincident följer du anvisningarna på Microsoft Support för att öppna en ny incident.
7. Redigera den **till** rad och **ämne** rad med den information som du har fått från Microsoft-supporten.
8. Azure Authenticator-appen bifogar loggfilen till e-post som du skickar. Beskriv problemet inträffar, Uppdatera lista över mottagare (valfritt) och skicka e-postmeddelandet.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Ta bort arbetskontot och låta din arbetsplats
Du kan ta bort arbetskonto som du skapade när som helst på följande sätt:

**Ta bort arbetskontot från menyn Inställningar**

1. Välj Kontohanteraren **Arbetskonto**.
2. På skärmen Arbetskonto i **allmänna inställningar**väljer **kontoinställningar – lämna nätverket på din arbetsplats**.
3. Välj **lämna** på den **Arbetsplatsanslutning** skärmen.
4. Klicka på **OK** när meddelandet ”är du säker på att du vill lämna arbetsplatsen” visas.
5. Detta säkerställer att du har tagit bort ditt arbetskonto från din arbetsplats.

> [!NOTE]
> Det rekommenderas att du inte använder alternativet Ta bort konto att ta bort ett arbetskonto som det här alternativet inte kanske fungerar i tidigare versioner av Android.
> 
> 

## <a name="uninstalling-the-app"></a>Avinstallera appen
På en Samsung Android-enhet och enheten administratörsbehörighet måste tas bort enligt följande innan du avinstallerar den 

1. Från **inställningar**under **System**väljer **säkerhet**.
2. Inom D**evice Administration**, klickar du på **enhetsadministratörer**. Kontrollera att kryssrutan bredvid **Azure Authenticator** är avmarkerad.

## <a name="troubleshooting"></a>Felsökning
Om du ser den **Keystore fel**, detta kan bero på att du inte har låset skärmdump set in med en PIN-kod. Undvik problemet genom att avinstallera Azure Authenticator-appen, konfigurera en PIN-kod för låsskärmen och installera om appen.

