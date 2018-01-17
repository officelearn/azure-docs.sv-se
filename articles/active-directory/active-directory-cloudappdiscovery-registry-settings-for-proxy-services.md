---
title: "Cloud App Discovery-registerinställningar för Proxy-tjänster | Microsoft Docs"
description: "Syftet med det här avsnittet är att förse dig med de steg som du behöver utföra för att ställa in den begärda porten på datorer som kör Cloud App Discovery-agenten."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 0e227d6e15789b29b40197a9ff71b2116312da78
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2018
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Cloud App Discovery-registerinställningar för Proxy-tjänster
Syftet med det här avsnittet är att information om hur du utför för att ange den nödvändiga porten på datorer som kör Cloud App Discovery-agenten. Cloud App Discovery-agenten har konfigurerats för att använda endast portarna 80 eller 443 som standard. Om du planerar att installera Cloud App Discovery i en miljö med en proxyserver som använder en anpassad port (varken 80 eller 443), måste du konfigurera dina agenter för att använda den här porten. Konfigurationen är baserad på en registernyckel.

> [!TIP] 
> Förbättringar av nu utan Agent Cloud App Discovery i Azure Active Directory (Azure AD), som har förbättrats genom att kolla [integrering med Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Ändra den port som används av datorn som kör Cloud App Discovery-agenten

1. Starta Registereditorn.
  ![Kör](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Navigera till eller skapa följande registernyckel: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud App Discovery\Endpoint**
3. Skapa en ny **multisträng** värde med namnet **portar**. 
  ![Ny](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Öppna den **redigera multisträng** dialogrutan, dubbelklicka på den **portar** värde.
5. I den **värdedata**, ange följande värden och lägger till alla anpassade portar som används av din organisation: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Redigera multisträng](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Klicka på **OK** att stänga den **redigera multisträng** dialogrutan.

## <a name="next-steps"></a>Nästa steg

* [Hur kan identifiera ej sanktionerade molnappar som används inom organisationen](active-directory-cloudappdiscovery-whatis.md) 

