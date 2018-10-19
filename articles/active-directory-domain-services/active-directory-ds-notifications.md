---
title: 'Azure Active Directory Domain Services: Meddelandeinställningar | Microsoft Docs'
description: Meddelandeinställningar för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: f5049d6092bfc6b3f70ebafdc501e02ad9fe7936
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431394"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Inställningar för meddelanden i Azure AD Domain Services

Meddelanden för Azure AD Domain Services kan du uppdateras så snart en hälsovarning har upptäckts på din hanterade domän.  

Den här funktionen är endast tillgänglig för hanterade domäner som inte finns på klassiska virtuella nätverk.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Hur du kontrollerar din Azure AD Domain Services e-postmeddelandeinställningarna

1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) på Azure portal
2. Välj din hanterade domän från tabellen
3. I det vänstra navigeringsfältet väljer **Meddelandeinställningar**

På sidan visas en lista med alla postmottagare av e-för e-postmeddelanden för Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>Hur ser ett e-postmeddelande ut?

Följande bild är ett exempel på ett e-postmeddelande:

![Exempel e-postmeddelande](.\media\active-directory-domain-services-alerts\email-alert.png)

E-postmeddelandet anger den hanterade domänen som finns på aviseringen, samt ge tid för identifiering och en länk till sidan om Azure AD Domain Services health i Azure-portalen.

> [!WARNING]
> Kontrollera alltid att e-postmeddelandet kommer från en verifierad Microsoft avsändare innan du klickar på länkarna i dina e-postmeddelanden. E-postmeddelanden kommer alltid från e-postmeddelandet azure-noreply@microsoft.com
>


## <a name="why-would-i-receive-email-notifications"></a>Varför skulle jag får e-postmeddelanden?

Azure AD Domain Services skickar e-postmeddelanden för viktiga uppdateringar om din domän.  Dessa meddelanden är endast för brådskande frågor som påverkar din tjänst och bör åtgärdas omedelbart. Varje e-postmeddelande utlöses av en avisering om din hanterade domän. Dessa aviseringar visas också på Azure portal och kan visas på den [sidan för Azure AD Domain Services health](active-directory-ds-check-health.md).

Azure AD Domain Services inte skickar e-postmeddelanden till den här listan för annons, uppdateringar eller försäljning syften.

## <a name="when-will-i-receive-email-notifications"></a>När får e-postmeddelanden?

Ett meddelande skickas omedelbart när en [ny avisering](active-directory-ds-troubleshoot-alerts.md) hittas på den hanterade domänen. Om aviseringen inte är löst skickas ett e-postmeddelande som en påminnelse var fjärde dag.

## <a name="who-should-receive-the-email-notifications"></a>Vem som ska få e-postmeddelanden?


 Vi rekommenderar att listan över e-postmottagare för Azure AD Domain Services ska bestå av personer som kommer administrera och göra ändringar i den hanterade domänen. Den här e-postlista bör betraktas som ”första svarare” till alla eventuella problem. Om du har fler än fem ytterligare e-postmeddelanden som du vill lägga till, rekommenderar vi att skapa en distributionslista för att lägga till i meddelandelistan över i stället.

Du kan lägga till upp till fem ytterligare e-postmeddelanden för meddelanden om Azure AD Domain Services. Dessutom kan du kan också välja att ha alla globala administratörer för din katalog och varje medlem i gruppen ”AAD DC-administratörer” ta emot e-postaviseringar till Azure AD Domain Services. Azure AD Domain Services kommer bara att skicka meddelanden till upp till 100 e-postadresser, inklusive en lista över globala administratörer och AAD DC-administratörer.


## <a name="how-to-add-an-additional-email-recipient"></a>Hur du lägger till en ytterligare e-postmottagare

> [!WARNING]
> När du ändrar inställningar för meddelanden ändrar du meddelandeinställningarna för hela hanterade domänen, inte bara själv.

1. Navigera till den [Azure AD Domain Services-sidan](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) på Azure portal.
2. Klicka på den hanterade domänen.
3. I det vänstra navigeringsfönstret klickar du på **Meddelandeinställningar**.
4. Lägg till ett e-postmeddelande, ange den e-postadress i tabellen ytterligare mottagare.
5. Klicka på ”Spara” i det övre navigeringsfältet.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Jag har fått ett e-postmeddelande för avisering men uppstod ingen avisering när jag har loggat in på Azure-portalen. Vad hände?

Om en avisering har lösts försvinner aviseringen från Azure-portalen. Den troligaste orsaken är att någon annan som tar emot e-postaviseringar löst avisering om din hanterade domän eller var automatiskt löst av Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Varför kan jag inte redigera inställningar för meddelanden?

Om det inte går att komma åt sidan meddelande inställningar i Azure-portalen har inte behörighet att redigera Azure AD Domain Services. Du måste kontakta din globala administratör för att antingen få behörighet att redigera resurser i Azure AD Domain Services eller tas bort från listan över mottagare.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Jag verkar inte får dessa meddelanden även om jag min e-postadress. Varför?

Kontrollera mappen för skräppost eller skräppost i din e-post för meddelandet och kontrollera att godkänna avsändaren (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Nästa steg
- [Lös aviseringar på din hanterade domän](active-directory-ds-troubleshoot-alerts.md)
- [Läs mer om Azure AD Domain Services](active-directory-ds-overview.md)
- [Kontakta produktteamet](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Kontakta oss
Kontakta Azure Active Directory Domain Services-produktteamet att [dela feedback eller support](active-directory-ds-contact-us.md).
