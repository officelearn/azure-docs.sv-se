---
title: Använda MySQL-databaser som PaaS på Azure-stacken | Microsoft Docs
description: Lär dig hur du kan distribuera MySQL Resource Provider och ange MySQL-databaser som en tjänst på Azure-stacken.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309843"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Använda MySQL-databaser på Microsoft Azure-stacken

Du kan distribuera MySQL-resursprovidern API för att använda MySQL-databaser som distribuerats i Azure-stacken. Mer information om resursprovidern API finns [Windows Azure Pack MySQL Resource Provider REST API-referens](https://msdn.microsoft.com/library/dn528442.aspx).

MySQL-databaser är vanliga på webbplatser och stöd för flera olika plattformar webbplats. Du kan till exempel skapa WordPress-webbplatser med hjälp av Web Apps-plattform som en tjänst (PaaS)-tillägg.

När du har distribuerat resursprovidern kan du:

* Skapa MySQL-servrar och databaser med hjälp av mallar för Azure Resource Manager distribution.
* Ange MySQL-databaser som en tjänst.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för MySQL-provider

Resursprovidern innehåller följande komponenter:

* **MySQL resource provider kortet virtuell dator (VM)**, vilket är en Windows virtuell dator som kör provider-tjänster.
* **Resursprovidern**, som bearbetar begäranden och ansluter till databasen resurser.
* **Servrar som är värd för MySQL servern**, som ger kapacitet för databaser som kallas värd för servrar. Du kan skapa MySQL instanser manuellt eller ge åtkomst till externa MySQL-instanser. Den [Azure Stack Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) har en exempelmall som du kan använda för att:

  * Skapa en MySQL-server.
  * Hämta och distribuera en MySQL-Server från Azure Marketplace.

> [!NOTE]
> Värd för servrar som är installerade på Azure-stacken måste integrerade system skapas från en klientprenumeration. De kan inte skapas från standard providern prenumerationen. De måste skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar fakturerbar virtuella datorer och måste ha licenser. Tjänstadministratören kan vara klient Prenumerationens ägare.

### <a name="required-privileges"></a>Behörigheter som krävs

System-kontot måste ha följande behörigheter:

* **Databas:** skapa, ta bort
* **Inloggning:** skapa, ställa in, drop, bevilja, återkalla  

## <a name="next-steps"></a>Nästa steg

[Distribuera MySQL-resursprovidern](azure-stack-mysql-resource-provider-deploy.md)
