---
title: Hantera Azure Automation-konto
description: Den här artikeln beskriver hur du hanterar konfigurationen av Automation-kontot, till exempel certifikatförnyelse, borttagning och felaktig konfiguration.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be1b35d2e7dc3d3e2efab825f318983e2943b0d2
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194367"
---
# <a name="manage-azure-automation-account"></a>Hantera Azure Automation-konto
Någon gång innan Automation-kontot går ut måste du förnya certifikatet. Om du tror att ditt Kör som-konto har komprometterats kan du ta bort och återskapa det. Det här avsnittet beskriver hur du utför dessa åtgärder.

## <a name="self-signed-certificate-renewal"></a>Förnya självsignerade certifikat
Det självsignerade certifikatet som du skapade för Kör som-kontot går ut ett år efter det datum då det skapades. Du kan förnya det när som helst innan det upphör att gälla. När du förnyar det behålls det aktuella giltiga certifikatet för att säkerställa att alla runbookflöden som placerats i kö eller som körs aktivt, och som autentiserar med Kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

> [!NOTE]
> Om du har konfigurerat ditt Kör som-konto för Automation att använda ett certifikat som utfärdats av din företagscertifikatutfärdare och du använder det här alternativet, så ersätts företagscertifikatet av ett självsignerat certifikat.

Du förnyar certifikatet genom att göra följande:

1. Öppna ditt Automation-konto på Azure Portal.

2. På **Automation-kontot** 
3. i fönstret **Kontoegenskaper** väljer du **Kör som-konton** under **Kontoinställningar**.

    ![Egenskapsrutan för Automation-konto](media/automation-manage-account/automation-account-properties-pane.png)
3. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill förnya certifikatet för.

4. I rutan **Egenskaper** för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/automation-manage-account/automation-account-renew-runas-certificate.png)

5. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto
Det här avsnittet beskriver hur du tar bort och sedan återskapar ett Kör som-konto eller ett klassiskt Kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort ett Kör som-konto eller ett klassiskt Kör som-konto kan du återskapa det på Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. På sidan **Automation-konto** väljer du **Kör som-konton**.

3. På egenskapssidan för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. Klicka på **Ta bort** i rutan **Egenskaper** för det valda kontot.

 ![Ta bort Kör som-konto](media/automation-manage-account/automation-account-delete-runas.png)

4. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

5. När kontot har tagits bort måste du återskapa det på egenskapssidan för **Kör som-konton** genom att välja alternativet Skapa för **Kör som-konto i Azure**.

 ![Återskapa Kör som-kontot för Automation](media/automation-manage-account/automation-account-create-runas.png)

## <a name="misconfiguration"></a>Felaktig konfiguration
Vissa konfigurationsobjekt som krävs för att Kör som-kontot eller det klassiska Kör som-kontot ska fungera kanske har tagits bort eller kanske inte skapades korrekt under den ursprungliga konfigurationen. Exempel på dessa objekt är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

I den föregående instansen och andra instanser av felaktiga konfigurationer identifierar Automation-kontot ändringarna och visar statusen *Ofullständig* på egenskapssidan för **Kör som-konton** för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/automation-manage-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande felmeddelande i rutan **Egenskaper** för kontot:

![Varningsmeddelande om ofullständig konfiguration av Kör som-konto](media/automation-manage-account/automation-account-runas-incomplete-config-msg.png).

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="next-steps"></a>Nästa steg
* Mer information om tjänstobjekt finns i [Programobjekt och tjänstobjekt](../active-directory/active-directory-application-objects.md).
* Mer information om rollbaserad åtkomstkontroll i Azure Automation finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* Mer information om certifikat och Azure-tjänster finns i [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).