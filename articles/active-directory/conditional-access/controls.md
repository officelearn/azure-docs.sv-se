---
title: Åtkomst kontroller i Azure Active Directory villkorlig åtkomst
description: Lär dig hur åtkomst kontroller i Azure Active Directory villkorlig åtkomst fungerar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: cac92da744b3d5b7aeaa325c7cc564a3d7e2abdd
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380816"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Vad är åtkomst kontroller i Azure Active Directory villkorlig åtkomst?

Med [Azure Active Directory (Azure AD) villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md)kan du styra hur behöriga användare får åtkomst till dina molnappar. I en princip för villkorlig åtkomst definierar du svaret ("gör detta") till orsaken till att principen utlöses ("när detta händer").

![Kontroll](./media/controls/10.png)

I sammanhang för villkorlig åtkomst,

- "**När detta händer kallas detta** **villkor**
- "**Gör sedan så här**" kallas **åtkomst kontroller**

Kombinationen av en villkors instruktion med dina kontroller representerar en princip för villkorlig åtkomst.

![Kontroll](./media/controls/61.png)

Varje kontroll är antingen ett krav som måste uppfyllas av personen eller systemet som loggar in, eller en begränsning för vad användaren kan göra efter att ha loggat in.

Det finns två typer av kontroller:

- **Bevilja kontroller** -till gång till grind
- **Sessions-kontroller** – för att begränsa åtkomst inom en session

I det här avsnittet beskrivs de olika kontroller som är tillgängliga i villkorlig åtkomst i Azure AD. 

## <a name="grant-controls"></a>Bevilja kontroller

Med bevilja kontroller kan du antingen blockera åtkomsten helt eller tillåta åtkomst med ytterligare krav genom att välja önskade kontroller. För flera kontroller kan du behöva:

- Alla valda kontroller som ska uppfyllas (*och*)
- En vald kontroll som ska uppfyllas (*eller*)

![Kontroll](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Du kan använda den här kontrollen för att kräva Multi-Factor Authentication för att få åtkomst till den angivna Cloud-appen. Den här kontrollen stöder följande Multi-Factor providers:

- Azure Multi-Factor Authentication
- En lokal Multi-Factor Authentication-provider som kombineras med Active Directory Federation Services (AD FS) (AD FS).

Genom att använda Multi-Factor Authentication kan du skydda resurser från att användas av en obehörig användare som har fått åtkomst till de primära autentiseringsuppgifterna för en giltig användare.

### <a name="compliant-device"></a>Kompatibel enhet

Du kan konfigurera villkorliga åtkomst principer som är enhets baserade. Syftet med en enhets-baserad princip för villkorlig åtkomst är att endast bevilja åtkomst till de valda molnappar från [hanterade enheter](require-managed-devices.md). Att kräva att en enhet markeras som kompatibel är ett alternativ som du behöver för att begränsa åtkomsten till hanterade enheter. En enhet kan markeras som kompatibel av Intune (för alla enhetens operativ system) eller av ditt MDM-system från tredje part för Windows 10-enheter. MDM-system från tredje part för andra typer av enhets operativ system än Windows 10 stöds inte. 

Enheten måste vara registrerad i Azure AD innan den kan markeras som kompatibel. För att registrera en enhet har du tre alternativ: 

- Azure AD-registrerade enheter
- Azure AD-anslutna enheter  
- Hybrid Azure AD-anslutna enheter

De här tre alternativen beskrivs i artikeln [Vad är en enhets identitet?](../devices/overview.md)

Mer information finns i [så här kräver du hanterade enheter för Cloud app-åtkomst med villkorlig åtkomst](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hybrid Azure AD-ansluten enhet

Att kräva en hybrid Azure AD-ansluten enhet är ett annat alternativ som du måste konfigurera enhets principer för villkorlig åtkomst. Detta krav avser Windows-datorer, bärbara datorer och företags surfplattor som är anslutna till en lokal Active Directory. Om det här alternativet väljs beviljar din princip för villkorlig åtkomst åtkomst till åtkomst försök som görs med enheter som är anslutna till din lokala Active Directory och din Azure Active Directory.  

Mer information finns i [Konfigurera Azure Active Directory enhets principer för villkorlig åtkomst](require-managed-devices.md).

### <a name="approved-client-app"></a>Godkänd klient app

Eftersom dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter kan du vilja skydda företags data som nås med hjälp av enheter även i de fall där de inte hanteras av dig.
Du kan använda [Intune App Protection-principer](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data oberoende av en lösning för hantering av mobila enheter (MDM).

Med godkända klient program kan du kräva en klient app som försöker komma åt dina molnappar för att stödja [Intune App Protection-principer](https://docs.microsoft.com/intune/app-protection-policy). Du kan till exempel begränsa åtkomsten till Exchange Online till Outlook-appen. En princip för villkorlig åtkomst som kräver godkända klient program kallas även för [app-baserad villkorlig åtkomst princip](app-based-conditional-access.md). En lista över godkända klient program som stöds finns i [godkända klient program krav](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>Skydds princip för app (för hands version)

Eftersom dina anställda använder mobila enheter för både personliga och arbetsrelaterade uppgifter kan du vilja skydda företags data som nås med hjälp av enheter även i de fall där de inte hanteras av dig.
Du kan använda [Intune App Protection-principer](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data oberoende av en lösning för hantering av mobila enheter (MDM).

Med appens skydds princip kan du begränsa åtkomsten till klient program som har rapporter ATS till Azure AD har tagit emot [Intunes skydds principer för appar](https://docs.microsoft.com/intune/app-protection-policy). Du kan till exempel begränsa åtkomsten till Exchange Online till Outlook-appen som har en princip för Intune App Protection. En princip för villkorlig åtkomst som kräver en app Protection-princip kallas även för en [app Protection-baserad princip för villkorlig åtkomst](app-protection-based-conditional-access.md). 

Enheten måste vara registrerad i Azure AD innan ett program kan markeras som princip skyddad.

En lista över de principer som är skyddade med klient program som stöds finns i [krav på skydds princip för appar](technical-reference.md#app-protection-policy-requirement).

### <a name="terms-of-use"></a>Användningsvillkor

Du kan kräva att en användare i din klient organisation samtycker till användnings villkoren innan de beviljas åtkomst till en resurs. Som administratör kan du konfigurera och anpassa användnings villkoren genom att ladda upp ett PDF-dokument. Om en användare omfattas av denna kontrolls åtkomst till ett program beviljas endast om användnings villkoren har överenskommits.

## <a name="custom-controls-preview"></a>Anpassade kontroller (förhands granskning)

Anpassade kontroller är en funktion i Azure Active Directory Premium P1-versionen. När du använder anpassade kontroller omdirigeras användarna till en kompatibel tjänst för att uppfylla ytterligare krav utanför Azure Active Directory. För att tillfredsställa den här kontrollen omdirigeras användarens webbläsare till den externa tjänsten, utför alla nödvändiga autentiserings-eller verifierings aktiviteter och dirigeras sedan tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och, om användaren har autentiserats eller verifierats, fortsätter användaren i flödet för villkorlig åtkomst.

Dessa kontroller tillåter användning av vissa externa eller anpassade tjänster som villkorliga åtkomst kontroller och utökar i allmänhet funktionerna för villkorlig åtkomst.

Leverantörer som för närvarande erbjuder en kompatibel tjänst är:

- [Duo-säkerhet](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Pinga identitet](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Om du vill ha mer information om dessa tjänster kan du kontakta providern direkt.

### <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Om du vill skapa en anpassad kontroll kontaktar du först den leverantör som du vill använda. Varje icke-Microsoft-provider har en egen process och krav för att registrera dig, prenumerera på eller på annat sätt bli en del av tjänsten och för att indikera att du vill integrera med villkorlig åtkomst. Vid det här tillfället tillhandahåller providern ett data block i JSON-format. Med den här informationen kan providern och villkorlig åtkomst samar beta för din klient, skapa den nya kontrollen och definiera hur villkorlig åtkomst kan avgöra om användarna har utfört verifiering med providern.

Det går inte att använda anpassade kontroller med identitets skydds automatisering som kräver Multi-Factor Authentication eller för att höja roller i Privileged Identity Manager (PIM).

Kopiera JSON-data och klistra sedan in den i den relaterade text rutan. Gör inga ändringar i JSON om du inte uttryckligen förstår den ändring du gör. Genom att göra en ändring kan du bryta anslutningen mellan leverantören och Microsoft och eventuellt låsa dig och dina användare från dina konton.

Alternativet för att skapa en anpassad kontroll finns i avsnittet **Hantera** på sidan för **villkorlig åtkomst** .

![Kontroll](./media/controls/82.png)

Om du klickar på **ny anpassad kontroll**öppnas ett blad med en text ruta för dina KONTROLLers JSON-data.  

![Kontroll](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Tar bort anpassade kontroller

Om du vill ta bort en anpassad kontroll måste du först se till att den inte används i någon princip för villkorlig åtkomst. När du är klar:

1. Gå till listan med anpassade kontroller
1. Klicka på...  
1. Välj **Ta bort**.

### <a name="editing-custom-controls"></a>Redigera anpassade kontroller

Om du vill redigera en anpassad kontroll måste du ta bort den aktuella kontrollen och skapa en ny kontroll med den uppdaterade informationen.

## <a name="session-controls"></a>Kontroller av sessioner

Session kontroller möjliggör begränsad upplevelse i en molnbaserad app. Session kontrollerna tillämpas av molnappar och förlitar sig på ytterligare information som tillhandahålls av Azure AD till appen om sessionen.

![Kontroll](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Använd app-framtvingade begränsningar

Du kan använda den här kontrollen för att kräva att Azure AD skickar enhets information till de valda molnappar. Med enhets informationen kan molnappar se om en anslutning initieras från en kompatibel eller domänansluten enhet. Den här kontrollen stöder endast SharePoint Online och Exchange Online som valda molnappar. När det här alternativet är markerat använder Cloud App enhets informationen för att tillhandahålla användare, beroende på enhetens tillstånd, med en begränsad eller fullständig upplevelse.

Du kan läsa mer här:

- [Aktivera begränsad åtkomst med SharePoint Online](https://aka.ms/spolimitedaccessdocs)
- [Aktivera begränsad åtkomst med Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst, se [KRÄV MFA för vissa appar med Azure Active Directory villkorlig åtkomst](app-based-mfa.md).
- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö, se [metod tips för villkorlig åtkomst i Azure Active Directory](best-practices.md).
