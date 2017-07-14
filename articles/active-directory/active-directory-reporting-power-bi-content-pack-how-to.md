---

title: "Så här använder du innehållspaketet för Azure Active Directory Power BI | Microsoft Docs"
description: "Lär dig hur du använder innehållspaketet för Azure Active Directory Power BI"
services: active-directory
author: MarkusVi
manager: femila
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/21/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: ab95f61e2d88f82bf2b24aea786c7dbadfb9c0fe
ms.contentlocale: sv-se
ms.lasthandoff: 06/22/2017


---
# Så här använder du innehållspaketet för Azure Active Directory Power BI
<a id="how-to-use-the-azure-active-directory-power-bi-content-pack" class="xliff"></a>

Det är viktigt för dig som IT-administratör att förstå hur dina användare anpassar sig till och använder Azure Active Directory-funktioner. Då kan du planera IT-infrastrukturen och kommunikationen för att öka användningen och för att få ut det mesta av AAD-funktionerna. Med Power BI-innehållspaketet för Azure Active Directory kan du ytterligare analysera dina data för att förstå hur du kan använda informationen och bättre tolka vad som händer med Azure Active Directory för de olika funktionerna som används ofta.  Du kan enkelt ladda ned det förskapade innehållspaketet och få information om alla aktiviteter i din Azure Active Directory med den omfattande visualiseringsupplevelsen som Power BI erbjuder med hjälp av integreringen av Azure Active Directory-API:er i Power BI. Du kan skapa en egen instrumentpanel och enkelt dela den med andra i din organisation. 

Det här avsnittet innehåller stegvisa instruktioner för hur du installerar och använder innehållspaketet i din miljö.

## Installation
<a id="installation" class="xliff"></a>  

**Så här installerar du Power BI-innehållspaketet:**

1. Logga in på [Power BI](https://app.powerbi.com/groups/me/getdata/services) med ditt Power BI-konto (det här är samma konto som ditt O365- eller Azure AD-konto).

2. Längst ned i det vänstra navigationsfönstret väljer du **Hämta data**.

    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/01.png)
 
3. I rutan **Tjänster** klickar du på **Hämta**.
   
    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/02.png)

4.  Sök efter **Azure Active Directory**.

    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/03.png)
 
5.  När du uppmanas anger du ditt klient-ID för Azure AD och klickar sedan på **Nästa**.

    > [!TIP] 
    > Ett snabbt sätt att hämta klient-ID för din Office 365-/Azure AD-klient är att logga in på Azure AD-portalen, öka detaljnivån i katalogen och kopiera ID:t från följande URL: https://manage.windowsazure.com/woodgroveonline.com#Workspaces/ActiveDirectoryExtension/Directory/<tenantid>/directoryQuickStart

    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/04.png) 

6.  Klicka på **Logga in**. 
 
    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/05.png) 



7.  Ange ditt användarnamn och lösenord och klicka sedan på **Logga in**.
 
    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/06.png) 

8.  Klicka på alternativet för att **godkänna** i appens dialogruta för medgivande.
 
9.  Klicka på instrumentpanelen för aktivitetsloggar i Azure Active Directory när den har skapats.
 
    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/08.png) 

## Vad kan jag göra med det här innehållspaketet?
<a id="what-can-i-do-with-this-content-pack" class="xliff"></a>

Här är en snabb genomgång av olika rapporter i innehållspaketet innan vi går in på det du kan göra med innehållspaketet. Rapportdata finns för de **senaste 30 dagarna**.

### Rapporter som ingår i den här versionen av innehållspaketet för Azure Active Directory-loggar
<a id="reports-included-in-this-version-of-azure-active-directory-logs-content-pack" class="xliff"></a>

**Appanvändning och trendrapporter**: Få insikter om de appar som används i din organisation, vilka som används mest och när de används. Du kan använda den här rapporten för att samla in insikter om hur en app som du nyligen distribuerade i organisationen används eller ta reda på vilka appar som är populära. På så sätt kan du förbättra användningen om du ser att en app inte används.

**Inloggningar efter plats och användare**: Ger information om alla inloggningar som utförs med hjälp av Azure Identity och information om användarnas identitet. Informationen hjälper dig att få djupare insikt i enskilda inloggningar och besvara frågor som:

- Varifrån loggade den här användaren in?
- Vilken användare har flest inloggningar och varifrån loggade användaren in? 
- Lyckades inloggningen?  
 
Du kan se ytterligare information genom att klicka på ett visst datum eller en viss plats.

**Unika användare per app**: Få en överblick över alla unika användare som använder en viss app. Detta omfattar endast användare som har loggat in till ett program ”*utan problem*”.

**Enhetsinloggningar**: Få en översikt över vilka operativsystem och webbläsare som användare använder i organisationen med detaljerad information om användarna som omfattar:

- Användarnamn
- IP-adress
- Plats 
- Inloggningsstatus 

Med den här rapporten kan du förstå de olika enhetsprofiler som används inom organisationen och bestämma principer för enheter baserat på vad som används

**SSPR-tratt**: Få förståelse för hur lösenordsåterställning fungerar i din organisation. Ta en förhandstitt på hur många lösenordsåterställningar som försökte utföras via SSPR-verktyget och hur många av dem som genomfördes. Lär dig mer om fel vid lösenordsåterställning med SSPR-tratten och förstå varför vissa fel uppstod. Den här rapporten ger en bättre förståelse för hur SSPR-verktyget används inom organisationen så att du kan fatta rätt beslut.

## Anpassa innehållspaketet för Azure AD Activity
<a id="customizing-azure-ad-activity-content-pack" class="xliff"></a>

**Ändra visualisering**: Du kan ändra ett visuellt objekt i en rapport genom att klicka på **Redigera rapport** och välja det önskade visuella objektet.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/09.png) 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/10.png) 

**Ta med ytterligare fält**: Du kan lägga till ett fält i rapporten eller ta bort det genom att välja det visuella objekt som du vill lägga till/ta bort från fältet. Jag lägger till fältet ”inloggningsstatus” i tabellvyn i exemplet nedan. 
 
![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/11.png) 

**Fäst visuella objekt på instrumentpanelen**: Du kan anpassa instrumentpanelen och ta med egna visuella objekt i rapporten och fästa på instrumentpanelen. I exemplet nedan lade jag till ett nytt filter som kallas ”Inloggningsstatus” och tog med det i rapporten. Jag ändrade även det visuella objektet från stapeldiagram till linjediagram och kan fästa det här nya visuella objektet på instrumentpanelen.

![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/12.png) 

![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/13.png) 
 

 


**Dela instrumentpanelen**: När du har skapat innehållet kan du dela instrumentpanelen med användare i organisationen. Kom ihåg att de kan se fälten som du markerat när du delar rapporten.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/14.png) 



## Schemalägga en daglig uppdatering av Power BI-rapporter
<a id="scheduling-a-daily-refresh-of-your-power-bi-report" class="xliff"></a>

Om du vill schemalägga en daglig uppdatering av en Power BI-rapport går du till **Datauppsättningar > Inställningar > Schemalägg uppdatering** och konfigurerar det enligt nedan.
 
![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/15.png) 

## Uppdatera till en nyare version av innehållspaketet
<a id="updating-to-newer-version-of-content-pack" class="xliff"></a>

Om du vill uppdatera ditt innehållspaket för att få en nyare version:

- Ladda ned det nya innehållspaketet och konfigurera enligt anvisningarna i den här artikeln.

- När du har konfigurerat det går du till **Datakälla > Inställningar > Autentiseringsuppgifter för datakälla** och anger dina autentiseringsuppgifter på nytt enligt anvisningarna nedan

    ![Innehållspaketet för Azure Active Directory Power BI](./media/active-directory-reporting-power-bi-content-pack-how-to/16.png) 

När den nya versionen av innehållspaketet fungerar kan du ta bort den gamla versionen vid behov genom att ta bort de underliggande rapporterna och de datauppsättningar som är associerade med innehållspaketet.

## Har du fortfarande problem?
<a id="still-having-issues" class="xliff"></a> 

Läs vår [felsökningsguide](active-directory-reporting-troubleshoot-content-pack.md). Läs de här [hjälpartiklarna](https://powerbi.microsoft.com/en-us/documentation/powerbi-service-get-started/) för allmän hjälp med Power BI.
 



