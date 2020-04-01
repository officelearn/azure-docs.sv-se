---
title: E-postmeddelanden för Azure AD Domain Services | Microsoft Dokument"
description: Lär dig hur du konfigurerar e-postmeddelanden för att varna dig om problem i en hanterad Azure Active Directory Domain Services-domän
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: ff5b0b430de53a67f4de0dacbd76a38a7de6e284
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475805"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurera e-postmeddelanden för problem i Azure Active Directory Domain Services

Hälsotillståndet för en Azure Active Directory Domain Services (Azure AD DS) hanterad domän övervakas av Azure-plattformen. Hälsostatussidan i Azure-portalen visar alla aviseringar för den hanterade domänen. För att se till att problem besvaras i tid kan e-postmeddelanden konfigureras för att rapportera om hälsoaviseringar så snart de har identifierats i azure AD DS-hanterade domänen.

Den här artikeln visar hur du konfigurerar e-postmeddelanden mottagare för en Azure AD DS hanterad domän.

## <a name="email-notification-overview"></a>Översikt över e-postmeddelande

Om du vill uppmärksamma problem med en Azure AD DS-hanterad domän kan du konfigurera e-postmeddelanden. Dessa e-postmeddelanden anger den Azure AD DS-hanterade domän som aviseringen finns på, samt ger tid för identifiering och en länk till hälsosidan i Azure-portalen. Du kan sedan följa de medföljande felsökningsråden för att lösa problemen.

Följande exempelmeddelande från e-postmeddelandet anger att en kritisk varning eller avisering har genererats på den hanterade Azure AD DS-domänen:

![Exempel på e-postmeddelande](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Kontrollera alltid att e-postmeddelandet kommer från en verifierad Microsoft-avsändare innan du klickar på länkarna i meddelandet. E-postmeddelandena kommer `azure-noreply@microsoft.com` alltid från adressen.

### <a name="why-would-i-receive-email-notifications"></a>Varför skulle jag få e-postmeddelanden?

Azure AD DS skickar e-postmeddelanden för viktiga uppdateringar om den hanterade domänen. Dessa anmälningar är endast till för brådskande frågor som påverkar tjänsten och bör åtgärdas omedelbart. Varje e-postmeddelande utlöses av en avisering på Azure AD DS-hanterad domän. Aviseringarna visas också i Azure-portalen och kan visas på [hälsosidan för Azure AD DS][check-health].

Azure AD DS skickar inte e-postmeddelanden för annonsering, uppdateringar eller försäljningsändamål.

### <a name="when-will-i-receive-email-notifications"></a>När får jag e-postmeddelanden?

Ett meddelande skickas omedelbart när en [ny avisering][troubleshoot-alerts] hittas på en Azure AD DS-hanterad domän. Om aviseringen inte är löst skickas ytterligare e-postmeddelanden som en påminnelse var fjärde dag.

### <a name="who-should-receive-the-email-notifications"></a>Vem ska få e-postmeddelanden?

Listan över e-postmottagare för Azure AD DS bör bestå av personer som kan administrera och göra ändringar i den hanterade domänen. Denna e-postlista bör ses som din "första responders" till alla varningar och problem.

Du kan lägga till ytterligare fem e-postmottagare för e-postmeddelanden. Om du vill ha fler än fem mottagare för e-postmeddelanden skapar du en distributionslista och lägger till den i meddelandelistan i stället.

Du kan också välja att alla *globala administratörer* i Azure AD-katalogen och alla medlemmar i gruppen *AAD DC-administratörer* får e-postmeddelanden. Azure AD DS skickar endast meddelanden till upp till 100 e-postadresser, inklusive en lista över globala administratörer och AAD DC-administratörer.

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Så här granskar du befintliga e-postmeddelanden eller lägger till ytterligare mottagare:

1. Sök efter och välj **Azure AD Domain Services**i Azure-portalen .
1. Välj din Azure AD DS-hanterade domän, till exempel *aaddscontoso.com*.
1. Välj **Meddelandeinställningar**till vänster i azure AD DS-resursfönstret . De befintliga mottagarna för e-postmeddelanden visas.
1. Om du vill lägga till en e-postmottagare anger du e-postadressen i tabellen ytterligare mottagare.
1. När du är klar väljer du **Spara** på den övre navigeringen.

> [!WARNING]
> När du ändrar meddelandeinställningarna uppdateras meddelandeinställningarna för hela Azure AD DS-hanterade domänen, inte bara dig själv.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Jag fick ett e-postmeddelande för en avisering, men när jag loggade in på Azure-portalen fanns det ingen avisering. Vad hände?

Om en avisering har åtgärdats rensas aviseringen från Azure-portalen. Den mest sannolika orsaken är att någon annan som tar emot e-postmeddelanden löste aviseringen på Azure AD DS-hanterad domän, eller att den löstes automatiskt av Azure-plattformen.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Varför kan jag inte redigera meddelandeinställningarna?

Om du inte kan komma åt sidan meddelandeinställningar i Azure-portalen har du inte behörighet att redigera den hanterade Azure AD DS-domänen. Du måste kontakta en global administratör för att antingen få behörighet att redigera Azure AD DS-resurs eller tas bort från mottagarlistan.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Jag verkar inte få e-postmeddelanden trots att jag angav min e-postadress. Varför det?

Kontrollera din skräppost- eller skräppostmapp i din e-post efter `azure-noreply@microsoft.com`meddelandet och se till att avsändaren av .

## <a name="next-steps"></a>Nästa steg

Mer information om felsökning av några av de problem som kan rapporteras finns i [Lösa aviseringar på en Azure AD DS-hanterad domän][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
