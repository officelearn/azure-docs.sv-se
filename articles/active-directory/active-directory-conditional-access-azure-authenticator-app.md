---
title: Azure Authenticator för Android | Microsoft Docs
description: Microsoft Azure Authenticator-appen kan användas för att logga in för att få åtkomst till arbetsresurser. Azure Authenticator-appen meddelar dig om en väntande tvåfaktorsautentisering verifieringsbegäran genom att visa en avisering till din mobila enhet.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.component: protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: femila
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: fbd728ec8cd2c8c4cd7ca74ecd84fd4d0d41cbd0
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055821"
---
# <a name="azure-authenticator-for-android"></a>Azure Authenticator för Android
IT-administratören kanske har rekommenderat dig att använda Microsoft Azure Authenticator för att logga in för att komma åt dina arbetsresurser. Det här programmet innehåller de här två alternativen inloggning:

* Multi-Factor Authentication kan du skydda dina arbets- eller skolkonto konton med tvåstegsverifiering. Du loggar in med något du känner (t.ex, ditt lösenord) och skyddar kontot ytterligare med något du har (en säkerhetsnyckel från den här appen). Azure Authenticator-appen meddelar dig om en väntande tvåfaktorsautentisering verifieringsbegäran genom att visa en avisering till din mobila enhet. Du behöver bara visa begäran i appen och tryck verifiera eller avvisa. Du kan också uppmanas att ange lösenordet som visas i appen.
* Arbetskonto kan du göra din Android-telefon eller surfplatta till en betrodd enhet och tillhandahålla enkel inloggning (SSO) för företagets program. IT-administratören kan kräva att du kan lägga till ett arbetskonto för att komma åt företagets resurser. Enkel inloggning kan du logga in en gång och automatiskt få tillgång till för alla program som ditt företag har gjorts tillgängliga för dig.

## <a name="installing-the-azure-authenticator-app"></a>Installera Azure Authenticator-appen
Du kan installera Azure Authenticator-appen från Google Play Store.
Anvisningarna för att lägga till arbetskonto från Samsung Android-enhet jämfört med en Samsung Android-enhet är något annorlunda. Anvisningar för både listas nedan.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Att lägga till arbetskontot från Samsung Android-enhet
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Att lägga till arbetskonto via appen startsidan
Följande instruktioner gäller att Samsung GS3 och ovanför telefoner eller meddelande 2 och senare surfplattor.

1. På startsidan i appen, accepterar du licensavtalet (EULA).
2. På skärmen aktivera konto klickar du på snabbmenyn till höger och välj **Arbetskonto**.
3. På Lägg till konto skärmen Välj ** Work konto **.
4. Klicka på Aktivera enhetens administratör skärm **aktivera**.
5. På skärmen sekretesspolicy markerar du kryssrutan och klicka på **Bekräfta**.
6. Ange användar-ID som tillhandahålls av din organisation och klicka på skärmen Anslut till arbetsplatsen **ansluta**.
7. Om du vill logga in på Azure Authenticator-appen, ange ditt organisationskonto och lösenord och klicka på **logga in**.
8. Nästa skärm som visar information om multifaktorautentisering (MFA) är för ytterligare säkerhet och är valfritt. Den här skärmen visas om ditt arbete eller din skola kräver andra faktor-autentisering för att skapa arbetskonto. Den innehåller instruktioner för att kontrollera ditt konto.
9. Workplace Join-skärmen visar meddelandet ”**ansluter till din arbetsplats**”. Azure authenticator-appen försöker att ansluta din enhet till din arbetsplats.
10. Du bör se meddelandet Arbetsplatsanslutna på nästa skärm.

> [!NOTE]
> Du får ett enda arbetskonto på din enhet.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Att lägga till arbetskontot från inställningsmenyn
När du har installerat Azure Authenticator-appen, kan du också skapa ett arbetskonto från Android Kontoansvariga.

1. Från menyn Inställningar navigerar du till **konton** och klicka på **Lägg till konto**.
2. Följ steg 3 – 10 i den proceduren, lägger till arbetskonto via appen startsidan, att lägga till ett arbetskonto.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Att lägga till arbetskontot från en Samsung Android-enhet
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Att lägga till arbetskonto via appen startsidan
1. På startsidan i appen, accepterar du licensavtalet (EULA).
2. På skärmen aktivera konto klickar du på snabbmenyn till höger och välj **Arbetskonto**.
3. På skärmen konton klickar du på **Lägg till konto**.
4. Om du ser skärmen konton, klickar du på **Lägg till konto**. Om ett arbetskonto redan har skapats tidigare, visas en synkronisering skärmbild som visar den befintliga arbetskonto. Du kan behålla arbetskontot med bakåtpilen på startsidan. Alternativt kan du välja kontot som ska ta bort och återskapa en ny work-konto på den till arbetsplatsen skärm, ange användar-ID som tillhandahålls av din organisation och på Join.
5. Om du vill logga in på Azure Authenticator-appen, ange ditt organisationskonto och lösenord och klicka på **logga in**.
6. Nästa skärm som visar information om multifaktorautentisering (MFA) är för ytterligare säkerhet och är valfritt. Den här skärmen visas om ditt arbete eller din skola kräver andra faktor-autentisering för att skapa arbetskonto. Den innehåller instruktioner för att kontrollera ditt konto.
7. Klicka på **OK** på nästa skärm. Ändra inte certifikatets namn.
   meddelandet ”ansluta till din arbetsplats”. Azure authenticator-appen försöker att ansluta din enhet till din arbetsplats.
   Du bör se meddelandet Arbetsplatsanslutna på nästa skärm.

> [!NOTE]
> Du får ett enda arbetskonto på din enhet.
> 
> 

När du har installerat Azure Authenticator-appen, kan du också skapa ett arbetskonto från Android Kontoansvariga.

1. Från den **inställningar** menyn konton och klickar på **Lägg till konto**.
2. Följ steg 2-7 i den proceduren, lägger till arbetskonto via den appen home skärmen **, om du vill lägga till ett arbetskonto.

### <a name="how-to-find-out-which-version-is-installed"></a>Ta reda vilken version är installerad
1. Du kan se vilken version av Azure Authenticator-appen och associerade tjänstversioner är installerade på din enhet.
2. I popup-menyn, klickar du på **om**.
3. Skärmen om visar tjänster på appen och de versioner som är installerad på din enhet.

### <a name="sending-log-files-to-report-issues"></a>Skicka loggfiler att rapportera problem
1. Följ anvisningarna på Microsoft Support att rapportera en incident med Azure Authenticator-appen, hämta incidentnummer och skicka loggfiler mot tilldelade incidentnumret på följande sätt:
2. I popup-menyn, klickar du på **loggning**.
3. Om du har en öppen incident med Microsoft Support, anteckna incidentnumret (du behöver det för ett senare steg). Om du inte redan har skapat ett supportärende och vill ha hjälp, följer du anvisningarna på [Microsoft Support](https://support.microsoft.com/contactus) att öppna en ny incident.
4. Klicka på skärmen för loggning **Skicka nu**.
5. Välj den e-postleverantör som du vill använda.
6. Om du redan har en öppen incident Microsoft Support kontakta supportteknikern som tilldelats problemet att ta reda på hur du skickar loggdata och associera den med ditt ärende. Supportteknikern ger du informationen om e-postadress och ämne. Om du inte redan har ett supportärende följer du anvisningarna på Microsoft Support att öppna en ny incident.
7. Redigera den **till** rad och **ämne** rad med den information som du har fått från Microsoft Support.
8. Azure Authenticator-appen bifogar loggfilen till e-postadressen som du skickar. Beskriv problemet du upplever, uppdatera mottagarlista (valfritt) och skicka e-postmeddelandet.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Ta bort arbetskontot och låta din arbetsplats
Du kan ta bort det arbetskonto som du skapade när som helst på följande sätt:

**Att ta bort arbetskontot från menyn Inställningar**

1. Hanteraren för kontosäkerhet väljer **Arbetskonto**.
2. På skärmen Arbetskonto i **allmänna inställningar**väljer **kontoinställningar – lämna din arbetsplats**.
3. Välj **lämna** på den **Arbetsplatsanslutning** skärmen.
4. Klicka på **OK** när meddelandet ”är du säker på att du vill lämna arbetsplats” visas.
5. Detta säkerställer att du har tagit bort ditt arbetskonto från din arbetsplats.

> [!NOTE]
> Du rekommenderas att du inte använder alternativet Ta bort konto att ta bort ett arbetskonto som det här alternativet inte kanske fungerar i vissa tidigare versioner av Android.
> 
> 

## <a name="uninstalling-the-app"></a>Avinstallera appen
På en Samsung Android-enhet, enhetens administratörsbehörighet måste tas bort enligt följande innan du avinstallerar den 

1. Från **inställningar**under **System**väljer **Security**.
2. Inom D**enhetsmodell Administration**, klickar du på **enhetsadministratörer**. Kontrollera att kryssrutan bredvid **Azure Authenticator** är avmarkerad.

## <a name="troubleshooting"></a>Felsökning
Om du ser den **Keystore fel**, detta kan bero på att du inte har Lås skärmen set upp med en PIN-kod. Undvik det här problemet, avinstallera Azure Authenticator-appen, konfigurera en PIN-kod för låsskärmen och installera om appen.

