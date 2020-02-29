---
title: 'Azure AD Connect Cloud Provisioning agent: automatisk uppgradering | Microsoft Docs'
description: Den här artikeln beskriver den inbyggda funktionen för automatisk uppgradering i Azure AD Connect Cloud Provisioning-agenten.
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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190321"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud Provisioning agent: automatisk uppgradering

Att se till att din Azure Active Directory (Azure AD) Connect-agenten för att ansluta till molnet är alltid uppdaterad är enkelt med funktionen för automatisk uppgradering.

Agenten installeras här: "program files\Azure AD Connect Agent\AADConnectProvisioningAgent.exe"

Kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent fil version](media/how-to-automatic-upgrade/agent1.png)

Agent uppdatering installeras här: "program files\Azure AD Connect Provisioning agent Updater\AzureADConnectAgentUpdater.exe"

Kontrol lera din version genom att högerklicka på den körbara filen och välja egenskaper och sedan information.

![Agent uppdaterings version](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Avinstallera agenten
Om du vill ta bort agenten går du till **Avinstallera eller ändra ett program** och avinstallerar följande:

- **Microsoft Azure AD ansluta Agent uppdatering**
- **Microsoft Azure AD ansluta etablerings agent**
- **Microsoft Azure AD ansluta Agent paket för etablering**

![Borttagning av agent](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect Cloud revisioner?](what-is-cloud-provisioning.md)

