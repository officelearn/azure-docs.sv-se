---
title: Installera Azure AD Power BI-innehållspaketet | Microsoft Docs
description: Lär dig hur du installerar zure AD Power BI-innehållspaketet
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: fd5604eb-1334-4bd8-bfb5-41280883e2b5
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 314f1c1656485f93942eb23f928cc66720a12565
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753548"
---
# <a name="quickstart-install-azure-active-directory-power-bi-content-pack"></a>Snabbstart: Installera innehållspaketet för Azure Active Directory Power BI

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

Power BI-innehållspaketet för Azure Active Directory (Azure AD) ger dig möjligheten att visualisera rapportdata från din miljö. Du kan hämta det fördefinierade innehållspaketet och använda det för att rapportera alla aktiviteter i din katalog med hjälp av den omfattande visualiseringsupplevelse som Power BI erbjuder. Du kan även skapa en egen instrumentpanel och enkelt dela den med andra i din organisation. 

I den här snabbstarten lär du dig hur du installerar innehållspaketet.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att slutföra den här snabbstarten:

* Ett Power BI-konto. Det här är samma konto som ditt O365- eller Azure AD-konto. 
* Ditt Azure AD-klientorganisations-ID. Det här är **katalog-ID:t** för din katalog från [egenskapssidan](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) i Azure-portalen.
* Licens för Azure AD Premium (P1/P2). Se [Komma igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din version av Azure Active Directory.

## <a name="install-azure-ad-power-bi-content-pack"></a>Installera Azure AD Power BI-innehållspaket 

1. Logga in på [Power BI](https://app.powerbi.com/groups/me/getdata/services) med ditt Power BI-konto. Det här är samma konto som ditt O365- eller Azure AD-konto.

2. Sök efter **Azure Active Directory-aktivitetsloggar** på sidan **Appar** och välj **Hämta nu**. 

   ![Innehållspaketet för Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/getitnow.png) 
    
3. I popup-fönstret anger du ditt Azure AD-klientorganisations-ID, ange **7** för det antal dagar som det ska köras frågor mot och välj sedan **Nästa**.
    
   ![Innehållspaketet för Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/connect.png) 

4. När din instrumentpanel för Azure Active Directory-aktivitetsloggar har skapats väljer du den.

   ![Innehållspaketet för Azure Active Directory Power BI](./media/quickstart-install-power-bi-content-pack/dashboard.png) 
    
## <a name="next-steps"></a>Nästa steg

* [Använda Power BI-innehållspaket](howto-power-bi-content-pack.md).
* [Felsöka fel i innehållspaket](troubleshoot-content-pack.md).
