---
title: "Kontrollerna i Azure Active Directory för villkorlig åtkomst | Microsoft Docs"
description: "Lär dig hur kontrollerna i Azure Active Directory för villkorlig åtkomst fungerar."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: be3631db20ae744965f9f6677c536ade45e34c49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="controls-in-azure-active-directory-conditional-access"></a>Kontrollerna i Azure Active Directory för villkorlig åtkomst 

Med [villkorlig åtkomst i Azure Active Directory (AD Azure)](active-directory-conditional-access-azure-portal.md), du kan styra hur behöriga användare åtkomst till dina molnappar. I en princip för villkorlig åtkomst definierar du svaret (”gör”) till ett visst villkor (”när detta sker”). I samband med villkorlig åtkomst 

- ”**När detta sker**” kallas **condition-instruktion**

- ”**Gör du så här**” kallas **kontroller**

![Kontrollen](./media/active-directory-conditional-access-controls/11.png)

Kombinationen av en condition-instruktion med kontrollerna representerar en princip för villkorlig åtkomst.

![Kontrollen](./media/active-directory-conditional-access-controls/12.png)

Varje kontroll är antingen ett krav som måste uppfyllas av personen eller system som loggar in eller en begränsning på vilka användare kan göra när du har loggat in. 

Det finns två typer av kontroller: 

- **Bevilja styr** - gate-åtkomst

- **Sessionen kontroller** - om du vill begränsa åtkomsten i en session

Det här avsnittet beskrivs de olika kontrollerna som är tillgängliga i Azure AD villkorlig åtkomst. 

## <a name="grant-controls"></a>Bevilja kontroller

Med grant-kontroller kan du blockera åtkomst helt och hållet eller tillåter åtkomst med ytterligare krav genom att välja önskad kontroller. Du kan kräva för flera kontroller:

- Alla markerade kontroller uppfylls (*och*) 
- En vald kontroll uppfylls (*eller*)

![Kontrollen](./media/active-directory-conditional-access-controls/17.png)



### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Du kan använda den här kontrollen för att kräva multifaktorautentisering att komma åt den angivna molnappen. Den här kontrollen har stöd för följande Multi-Factor-providers: 

- Azure Multi-Factor Authentication 

- En lokal flerfunktionsautentiseringsleverantör, tillsammans med Active Directory Federation Services (AD FS).
 
Använda Multi-Factor authentication skyddar resurser från används av en obehörig användare som kan ha fick tillgång till de primära autentiseringsuppgifterna för en giltig användare.



### <a name="compliant-device"></a>Kompatibel enhet

Du kan konfigurera principer för villkorlig åtkomst som baseras på enheten. Syftet med en princip för enhetsbaserad villkorlig åtkomst är att ge åtkomst till resurserna som är konfigurerade för endast från betrodda enheter. Kräver en kompatibel enhet är ett alternativ som du behöver definiera är en betrodd enhet. Mer information finns i [ställa in Azure Active Directory enhetsbaserad villkorliga åtkomstprinciper](active-directory-conditional-access-policy-connected-applications.md).

### <a name="domain-joined-device"></a>Domänansluten enhet

Kräver en domänansluten enhet är ett annat alternativ som du behöver konfigurera principer för enhetsbaserad villkorlig åtkomst. Det här kravet refererar till Windows-arbetsstationer, bärbara datorer och enterprise-surfplattor som är anslutna till en lokal Active Directory. Mer information finns i [ställa in Azure Active Directory enhetsbaserad villkorliga åtkomstprinciper](active-directory-conditional-access-policy-connected-applications.md).





### <a name="approved-client-app"></a>Godkända-klientappen

Eftersom dina anställda använder mobila enheter för både personliga och arbetsuppgifter, kanske du vill ha möjlighet att skydda företagsdata som nås med enheter även i de fall där de inte hanteras av dig.
Du kan använda [skyddsprinciper för Intune app](https://docs.microsoft.com/intune/app-protection-policy) för att skydda företagets data som är oberoende av någon lösning för hantering av mobila enheter (MDM).


Med godkända klientappar du behöver ett klientprogram som försöker få åtkomst till dina molnappar för att stödja [skyddsprinciper för Intune app](https://docs.microsoft.com/intune/app-protection-policy). Du kan exempelvis begränsa åtkomsten till Exchange Online för Outlook-appen. En villkorlig åtkomstprincip som kräver godkända klientprogram kallas även [princip för app-baserad villkorlig åtkomst](active-directory-conditional-access-mam.md). En lista över godkända klientprogram som stöds, se [godkända kravet på klienten app](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement).


### <a name="terms-of-use"></a>Användningsvillkor

Du kan kräva en användare i din klient samtycker till användarvillkoren innan de ges tillgång till en resurs. Som administratör kan du konfigurera och anpassa användningsvillkoren genom att ladda upp ett PDF-dokument. Om en användare som ingår beviljas omfånget för den här kontrollen åtkomst till ett program endast om användningsvillkoren har godkänts. 


### <a name="custom-controls"></a>Anpassade kontroller 

Du kan skapa anpassade kontroller för villkorlig åtkomst som dirigera användarna till en kompatibel tjänsten för att uppfylla ytterligare krav utanför Azure Active Directory. På så sätt kan du använda vissa externa multifaktorautentisering och verifiering providers genomdriva regler för villkorlig åtkomst eller för att skapa egna anpassade tjänster. För att uppfylla den här kontrollen användarens webbläsare omdirigeras till externa tjänsten, utför alla nödvändiga autentisering eller verifiering aktiviteter och dirigeras sedan tillbaka till Azure Active Directory. Om användaren har har autentiserad eller verifieras kan fortfarande användaren i flöde för villkorlig åtkomst. 

## <a name="custom-controls"></a>Anpassade kontroller

Anpassade kontroller i villkorlig åtkomst dirigera användarna till en kompatibel tjänsten för att uppfylla ytterligare krav utanför Azure Active Directory. För att uppfylla den här kontrollen användarens webbläsare omdirigeras till externa tjänsten, utför alla nödvändiga autentisering eller verifiering aktiviteter och dirigeras sedan tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och om användaren har har autentiserad eller verifieras kan användaren fortfarande i flöde för villkorlig åtkomst.

De här kontrollerna tillåter användning av vissa externa eller anpassade tjänster som villkorlig åtkomstkontroller och vanligtvis utöka funktionerna i villkorlig åtkomst.

Providers för närvarande erbjuder en kompatibel tjänst är:

- Duo säkerhet

- RSA

- Trusona

Mer information om dessa tjänster kontakta providers.

### <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Om du vill skapa en anpassad kontroll, bör du kontakta den provider som du vill använda. Varje icke-Microsoft-leverantör har sin egen process och krav för att logga, prenumerera eller annars blir en del av tjänsten och att indikera att du vill integrera med villkorlig åtkomst. Vid den punkten ger providern dig ett datablock i JSON-format. Dessa data kan providern och villkorlig åtkomst att fungera ihop för din klient, skapas den nya kontrollen och definierar hur villkorlig åtkomst kan berätta om dina användare har utförts verifiering med providern.

Kopiera JSON-data och klistra in den i textrutan relaterade. Du inte göra några ändringar i JSON om du uttryckligen förstår ändringen som du gör. Gör alla ändringar kan bryta anslutningen mellan provider och Microsoft och potentiellt lås du och användarna utanför dina konton.

Alternativet för att skapa en anpassad kontroll som finns i den **hantera** avsnitt i den **villkorlig åtkomst** sidan.

![Kontrollen](./media/active-directory-conditional-access-controls/82.png)

Klicka på **nya anpassade kontrollen**, öppnas ett blad med en textruta för JSON-data för kontrollen.  


![Kontrollen](./media/active-directory-conditional-access-controls/81.png)


### <a name="deleting-custom-controls"></a>Ta bort anpassade kontroller

Om du vill ta bort en anpassad kontroll måste du först se till att det inte används i principen för villkorlig åtkomst. Utför en gång:

1. Gå till listan över anpassade kontroller

2. Klicka på...  

3. Välj **Ta bort**.

### <a name="editing-custom-controls"></a>Redigera anpassade kontroller

Om du vill redigera en anpassad kontroll måste du ta bort den aktuella kontrollen och skapa en ny kontroll med den uppdaterade informationen.




## <a name="session-controls"></a>Sessionen kontroller

Sessionen kontroller Aktivera begränsad erfarenhet inom en molnappen. Sessionen kontroller tillämpas av molnappar och förlitar sig på ytterligare information som tillhandahålls av Azure AD App om sessionen.

![Kontrollen](./media/active-directory-conditional-access-controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Använd appbegränsningar tillämpas

Du kan använda den här kontrollen kräver Azure AD för att skicka information om de enheten till molnappen. Detta hjälper molnappen om användaren kommer från en kompatibel enhet eller en domänansluten enhet. Den här kontrollen är för närvarande stöds endast med SharePoint som molnappen. SharePoint använder enhetsinformationen för att ge användarna en begränsad eller fullständig upplevelse beroende på enhetens tillstånd.
Mer information om hur du kräver begränsad åtkomst med SharePoint finns [styra åtkomsten från ohanterade enheter](https://aka.ms/spolimitedaccessdocs).



## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
