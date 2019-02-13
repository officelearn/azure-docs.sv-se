---
title: Vad är åtkomstkontroller i Azure Active Directory villkorlig åtkomst? | Microsoft Docs
description: Lär dig hur åtkomstkontroll i Azure Active Directory villkorlig åtkomst för arbete.
services: active-directory
keywords: conditional access to apps, conditional access with Azure AD, secure access to company resources, conditional access policies
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53187bfbc32d618663f1c8e3e6fb232d748b6b99
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169130"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Vad är åtkomstkontroller i Azure Active Directory villkorlig åtkomst?

Med [villkorlig åtkomst i Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina appar i molnet. I en princip för villkorlig åtkomst definierar du svaret (”gör du”) att orsaken för att utlösa principen (”när detta sker”).

![Kontroll](./media/controls/10.png)

I samband med villkorlig åtkomst

- ”**Om det här händer**” kallas **villkor**

- ”**Gör detta**” kallas **åtkomstkontroller**

Kombinationen av en villkorssatsen med din kontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/controls/61.png)

Alla kontroller är antingen ett krav som måste uppfyllas av personen eller system som loggar in, eller en begränsning på vad användaren kan göra när du loggar in.

Det finns två typer av kontroller:

- **Bevilja kontroller** – du förhindrar åtkomst

- **Sessionskontroller** – om du vill begränsa åtkomsten i en session

Det här avsnittet beskrivs de olika kontroller som är tillgängliga i Azure AD villkorsstyrd åtkomst. 

## <a name="grant-controls"></a>Bevilja kontroller

Med bevilja kontroller du antingen blockera åtkomsten helt eller tillåta åtkomst med ytterligare krav genom att välja önskad kontroller. Du kan kräva för flera kontroller:

- Alla markerade kontroller är uppfyllda (*och*)
- En vald kontroll uppfylls (*eller*)

![Kontroll](./media/controls/17.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Du kan använda den här kontrollen för att kräva multifaktorautentisering att få åtkomst till angivna molnappen. Den här kontrollen stöder följande Multi-Factor Authentication-providers:

- Azure Multi-Factor Authentication

- En lokal Multi-Factor authentication-provider i kombination med Active Directory Federation Services (AD FS).

Multi-Factor Authentication hjälper till att skydda resurser från används av en obehörig användare som kan ha fått åtkomst till de primära autentiseringsuppgifterna för en giltig användare.

### <a name="compliant-device"></a>Kompatibel enhet

Du kan konfigurera principer för villkorlig åtkomst som baseras på enheten. Målet med principer för enhetsbaserad villkorlig åtkomst är att endast bevilja åtkomst till de valda molnapparna från [hanterade enheter](require-managed-devices.md). Kräver en enheten är markerad som kompatibel är ett alternativ som du har för att begränsa åtkomsten till hanterade enheter. En enhet kan markeras som kompatibel av Intune (för alla enhetens operativsystem) eller av tredje parts MDM-systemet för Windows 10-enheter. Tredje parts MDM-system för enhetstyper operativsystem än Windows 10 stöds inte. 

Enheten behöver registreras med Azure AD innan den kan vara markerad som kompatibel. Om du vill registrera en enhet har tre alternativ: 

- [Azure AD-registrerade enheter](../devices/overview.md#azure-ad-registered-devices)
- [Azure AD-anslutna enheter](../devices/overview.md#azure-ad-joined-devices)  
- [Hybrid Azure AD-anslutna enheter](../devices/overview.md#hybrid-azure-ad-joined-devices)

Mer information finns i [kräva hanterade enheter för åtkomst till molnet appen med villkorlig åtkomst](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hybrid Azure AD-domänansluten enhet

Kräver att en Hybrid Azure AD-domänansluten enhet är ett annat alternativ som du måste konfigurera principer för enhetsbaserad villkorlig åtkomst. Det här kravet refererar till Windows-arbetsstationer, bärbara datorer och enterprise-surfplattor som är anslutna till en lokal Active Directory. Om det här alternativet väljs, beviljar principer för villkorlig åtkomst åtkomst till åtkomstförsök med enheter som är anslutna till din lokala Active Directory och Azure Active Directory.  

Mer information finns i [ställa in principer för Azure Active Directory-enhetsbaserad villkorlig åtkomst](require-managed-devices.md).

### <a name="approved-client-app"></a>Godkänd klientapp

Eftersom dina anställda använder mobila enheter för både personliga och arbetsuppgifter, kanske du vill ha möjlighet att skydda företagsdata som nås med enheter även i de fall där de inte hanteras av dig.
Du kan använda [Intunes appskyddsprinciper](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data som är oberoende av någon lösning för hantering av mobila enheter (MDM).

Med godkända appar, behöver du en klientapp som försöker få åtkomst till dina appar i molnet som stöd för [Intunes appskyddsprinciper](https://docs.microsoft.com/intune/app-protection-policy). Du kan exempelvis begränsa åtkomsten till Exchange Online för Outlook-appen. Principer för villkorlig åtkomst som kräver godkända klientappar kallas även [appbaserad villkorlig åtkomstprincip](app-based-conditional-access.md). En lista över godkända klientappar som stöds finns i [godkända kravet på klienten app](technical-reference.md#approved-client-app-requirement).

### <a name="terms-of-use"></a>Användningsvillkor

Du kan kräva att en användare i din klient samtycker till att användningsvillkoren innan de ges tillgång till en resurs. Som administratör kan du konfigurera och anpassa användningsvillkoren genom att överföra en PDF-dokumentet. Om en användare som ingår i beviljas omfånget för den här kontrollen åtkomst till ett program endast om det har har accepterat användningsvillkoren.

### <a name="custom-controls-preview"></a>Anpassade kontroller (förhandsversion)

Du kan skapa anpassade kontroller för villkorlig åtkomst som omdirigerar användarna till en kompatibel tjänst för att uppfylla ytterligare krav utanför Azure Active Directory. På så sätt kan du använda vissa externa multifaktorautentisering och verifiering leverantörer att genomdriva regler för villkorlig åtkomst eller för att skapa din egen anpassade tjänst. För att uppfylla den här kontrollen, en användares webbläsare omdirigeras till den externa tjänsten, utför alla nödvändiga autentisering eller validering aktiviteter och sedan omdirigeras tillbaka till Azure Active Directory. Om användaren har har autentiserats eller verifierats, fortsätter användaren i flödet för villkorlig åtkomst. 

## <a name="custom-controls"></a>Anpassade kontroller

Anpassade kontroller är en funktion i Azure Active Directory Premium P1-versionen. När du använder anpassade kontroller, omdirigeras användarna till en kompatibel tjänst för att uppfylla ytterligare krav utanför Azure Active Directory. För att uppfylla den här kontrollen, en användares webbläsare omdirigeras till den externa tjänsten, utför alla nödvändiga autentisering eller validering aktiviteter och sedan omdirigeras tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och om användaren har har autentiserad eller verifiera kan användaren fortfarande i flödet för villkorlig åtkomst.

Dessa kontroller kan användningen av vissa externa eller anpassade tjänster som de villkorlig åtkomst och allmänt utöka funktionerna i villkorlig åtkomst.

Leverantörer som för närvarande har en kompatibel tjänst är:

- [Duo säkerhet](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Mer information om dessa tjänster kontaktar du providers direkt.

### <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Du bör kontakta den provider som du vill använda för att skapa en anpassad kontroll. Varje icke-Microsoft-provider har en egen process och krav för att registrera dig, prenumerera på eller på annat sätt har blivit en del av tjänsten och för att indikera att du vill integrera med villkorlig åtkomst. I det här läget ger providern dig med ett datablock i JSON-format. Dessa data kan providern och villkorlig åtkomst fungerar tillsammans för din klient, skapar den nya kontrollen och definierar hur villkorlig åtkomst kan berätta om dina användare har utförts verifiering med providern.

Kopiera JSON-data och klistra in den i textrutan relaterade. Du inte göra några ändringar i JSON, såvida inte du uttryckligen förstår ändringen du gör. Gör några ändringar kan bryta anslutningen mellan providern och Microsoft och potentiellt låsa dig och dina användare från dina konton.

Alternativet för att skapa en anpassad kontroll som finns i den **hantera** delen av den **villkorlig åtkomst** sidan.

![Kontroll](./media/controls/82.png)

Klicka på **ny anpassad kontroll**, öppnas ett blad med en textruta för JSON-data för din kontroll.  

![Kontroll](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Ta bort anpassade kontroller

Om du vill ta bort en anpassad kontroll, måste du först kontrollera att det inte används i någon princip för villkorlig åtkomst. När det är klart:

1. Gå till listan anpassade kontroller

2. Klicka på...  

3. Välj **Ta bort**.

### <a name="editing-custom-controls"></a>Redigera anpassade kontroller

Om du vill redigera en anpassad kontroll, måste du ta bort den aktuella kontrollen och skapa en ny kontroll med den uppdaterade informationen.

## <a name="session-controls"></a>Sessionskontroller

Sessionskontroller aktiverar en begränsad upplevelse inom en molnapp. Sessionskontroller tillämpas av molnappar och förlitar sig på ytterligare information som tillhandahålls av Azure AD för att appen om sessionen.

![Kontroll](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Använd app-framtvingade begränsningar

Du kan använda den här kontrollen för att kräva Azure AD för att skicka enhetsinformation till de valda molnapparna. Informationen i gör det möjligt för moln-appar du behöver veta om en anslutning initieras från en kompatibel eller domänansluten enhet. Den här kontrollen stöder endast SharePoint Online och Exchange Online som valda molnappar. När du väljer använder molnappen enhetsinformationen för att ge användare, beroende på enhetens tillstånd, med en begränsad eller fullständig upplevelse.

Du kan läsa mer här:

- [Att aktivera begränsad åtkomst med SharePoint Online](https://aka.ms/spolimitedaccessdocs)

- [Att aktivera begränsad åtkomst med Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar principer för villkorlig åtkomst finns i [kräver MFA för specifika appar med villkorlig åtkomst i Azure Active Directory](app-based-mfa.md).

- Om du är redo att konfigurera principer för villkorsstyrd åtkomst för din miljö kan du läsa sidan om [metodtips för villkorsstyrd åtkomst i Azure Active Directory](best-practices.md).
