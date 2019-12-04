---
title: 'Azure AD Connect Cloud Provisioning agent: automatisk uppgradering | Microsoft Docs'
description: I det här avsnittet beskrivs den inbyggda funktionen för automatisk uppgradering i Azure AD Connect Cloud Provisioning-agenten.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794463"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud Provisioning agent: automatisk uppgradering

Se till att installationen av Azure AD Connect Cloud Provisioning-agenten alltid är uppdaterad har aldrig varit enklare med funktionen för **automatisk uppgradering** . Den här funktionen är aktive rad som standard och kan inte inaktive ras.

Agenten installeras här: **"program FILES\AZURE AD Connect Agent\AADConnectProvisioningAgent.exe"**

Du kan kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent fil version](media/how-to-automatic-upgrade/agent1.png)

Agent uppdatering installeras här: **"program FILES\AZURE AD Connect Provisioning agent Updater\AzureADConnectAgentUpdater.exe"**

Du kan kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent uppdaterings version](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>Avinstallera agenten
Om du vill ta bort agenten navigerar du till **Avinstallera eller ändra ett program** och avinstallerar följande:

- Microsoft Azure AD ansluta Agent uppdatering
- Microsoft Azure AD ansluta etablerings agent
- Microsoft Azure AD ansluta Agent paket för etablering

![Borttagning av agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)

