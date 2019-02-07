---
title: Använda MySQL-databaser som PaaS på Azure Stack | Microsoft Docs
description: Lär dig hur du kan distribuera MySQL-Resursprovidern och ange MySQL-databaser som en tjänst på Azure Stack.
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
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 6922571e4e816d04f6e116db582922cecc85542c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765474"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Använda MySQL-databaser på Microsoft Azure Stack

MySQL-databaser är vanliga på webbplatser och stöd för flera plattformar. Du kan till exempel skapa WordPress-webbplats med hjälp av Web Apps-plattformen som en tjänst (PaaS)-tillägg.

När du distribuerar resursprovidern, kan du:

* Skapa MySQL-servrar och databaser med hjälp av mallar för Azure Resource Manager-distribution.
* Ange MySQL-databaser som en tjänst.  

## <a name="mysql-resource-provider-adapter-architecture"></a>Adapter-arkitektur för MySQL-provider

Resursprovidern har följande komponenter:

* **MySQL resource provider nätverkskort virtuell dator (VM)**, vilket är en Windows Server VM som körs i tjänsterna.
* **Resursprovidern**, som bearbetar begäranden och kommer åt databasresurser.
* **Servrar som är värdar för MySQL-Server**, vilket ger kapacitet för databaser som kallas som är värd för servrar. Du kan skapa MySQL-instanser själv eller ge åtkomst till externa MySQL-instanser. Den [Azure Stack-Snabbstartsgalleriet](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) har en exempelmall som du kan använda för att:

  * Skapa en MySQL-server åt dig.
  * Hämta och distribuera en MySQL-Server från Azure Marketplace.

> [!NOTE]
> Som är värd för servrar som är installerade på Azure Stack måste integrerade system skapas från en klientprenumeration. De kan inte skapas från providern Standardprenumeration. De måste skapas från klientportalen eller från en PowerShell-session med en lämplig inloggning. Alla värdservrar är fakturerbara virtuella datorer och måste ha licenser. Tjänstadministratören kan vara ägare till den klient-prenumerationen.

### <a name="required-privileges"></a>Behörigheter som krävs

System-kontot måste ha följande behörigheter:

* **Databas:** skapa, ta bort
* **Inloggning:** skapa, ange, släppa, bevilja, återkalla  

## <a name="next-steps"></a>Nästa steg

[Distribuera MySQL-resursprovider](azure-stack-mysql-resource-provider-deploy.md)
