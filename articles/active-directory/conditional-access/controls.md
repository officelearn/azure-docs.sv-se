---
title: Anpassade kontroller i villkorlig åtkomst i Azure AD
description: Lär dig hur anpassade kontroller i Azure Active Directory villkorlig åtkomst fungerar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 02/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f1df037b66c72177a96f77231cee70782d04992
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2020
ms.locfileid: "77620718"
---
# <a name="custom-controls-preview"></a>Anpassade kontroller (förhands granskning)

Anpassade kontroller är en funktion i Azure Active Directory Premium P1-versionen. När du använder anpassade kontroller omdirigeras användarna till en kompatibel tjänst för att uppfylla ytterligare krav utanför Azure Active Directory. För att tillfredsställa den här kontrollen omdirigeras användarens webbläsare till den externa tjänsten, utför alla nödvändiga autentiserings-eller verifierings aktiviteter och dirigeras sedan tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och, om användaren har autentiserats eller verifierats, fortsätter användaren i flödet för villkorlig åtkomst.

Dessa kontroller tillåter användning av vissa externa eller anpassade tjänster som villkorliga åtkomst kontroller och utökar i allmänhet funktionerna för villkorlig åtkomst.

Leverantörer som för närvarande erbjuder en kompatibel tjänst är:

- [Duo-säkerhet](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [GSMA](https://mobileconnect.io/azure/)
- [Pinga identitet](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- [RSA](https://community.rsa.com/docs/DOC-81278)
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Thales (Gemalto)](https://resources.eu.safenetid.com/help/AzureMFA/Azure_Help/Index.htm)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Om du vill ha mer information om dessa tjänster kan du kontakta providern direkt.

## <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Om du vill skapa en anpassad kontroll kontaktar du först den leverantör som du vill använda. Varje icke-Microsoft-provider har en egen process och krav för att registrera dig, prenumerera på eller på annat sätt bli en del av tjänsten och för att indikera att du vill integrera med villkorlig åtkomst. Vid det här tillfället tillhandahåller providern ett data block i JSON-format. Med den här informationen kan providern och villkorlig åtkomst samar beta för din klient, skapa den nya kontrollen och definiera hur villkorlig åtkomst kan avgöra om användarna har utfört verifiering med providern.

Det går inte att använda anpassade kontroller med identitets skydds automatisering som kräver Multi-Factor Authentication eller för att höja roller i Privileged Identity Manager (PIM).

Kopiera JSON-data och klistra sedan in den i den relaterade text rutan. Gör inga ändringar i JSON om du inte uttryckligen förstår den ändring du gör. Genom att göra en ändring kan du bryta anslutningen mellan leverantören och Microsoft och eventuellt låsa dig och dina användare från dina konton.

Alternativet för att skapa en anpassad kontroll finns i avsnittet **Hantera** på sidan för **villkorlig åtkomst** .

![Kontroll](./media/controls/82.png)

Om du klickar på **ny anpassad kontroll**öppnas ett blad med en text ruta för dina KONTROLLers JSON-data.  

![Kontroll](./media/controls/81.png)

## <a name="deleting-custom-controls"></a>Tar bort anpassade kontroller

Om du vill ta bort en anpassad kontroll måste du först se till att den inte används i någon princip för villkorlig åtkomst. När du är klar:

1. Gå till listan med anpassade kontroller
1. Klicka på...  
1. Välj **Ta bort**.

## <a name="editing-custom-controls"></a>Redigera anpassade kontroller

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
