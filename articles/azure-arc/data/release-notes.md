---
title: Azure Arc-aktiverade data tjänster – viktig information
description: Senaste versions information
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: d22976254cc804ca53060fb284abde8e80a684e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319740"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Viktig information – Azure Arc-aktiverade data tjänster (för hands version)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="september-2020"></a>September 2020

Azure Arc-aktiverade data tjänster har publicerats för offentlig för hands version. Med ARC-aktiverade data tjänster kan du hantera data tjänster var som helst.

- SQL-hanterad instans
- PostgreSQL-storskalig

Mer information finns i [Vad är Azure Arc-aktiverade data tjänster?](overview.md)

### <a name="known-issues"></a>Kända problem

Följande problem gäller den här versionen:

* **Tar bort postgresql-server grupp**: om du har ändrat konfigurationen för Server gruppen eller instansen väntar du tills redigerings åtgärden har slutförts innan du tar bort en postgresql-server grupp för storskalig skalning.

* ** `azdata notebook run` kan Miss lyckas**: om du vill lösa det här problemet kan du köra `azdata notebook run` i en virtuell python-miljö. Det här problemet uppstår också vid ett misslyckat försök att skapa en SQL-hanterad instans eller en PostgreSQL Server grupp med hjälp av guiden Azure Data Studio distribution. I så fall kan du öppna antecknings boken och klicka på knappen **Kör alla** överst i antecknings boken.

## <a name="next-steps"></a>Nästa steg

> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc-rivstart med](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

[Installera klient verktyg](install-client-tools.md)

[Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)

[Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)

[Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)

## <a name="known-limitations-and-issues"></a>Kända begränsningar och problem

- SQL-hanterade instans namn får inte vara längre än 13 tecken
- Ingen uppgradering på plats för Azure Arc-datakontrollanten eller databas instanserna.
- De Arc-aktiverade datatjänstcontaineravbildningarna är inte signerade.  Du kan behöva konfigurera dina Kubernetes-noder så att de tillåter hämtning av osignerade containeravbildningar.  Om du till exempel använder Docker som container runtime, kan du ange miljövariabeln DOCKER_CONTENT_TRUST = 0 och starta om.  Andra containerkörningar har liknande alternativ, som i [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Det går inte att skapa Azure Arc-aktiverade SQL-hanterade instanser eller PostgreSQL för storskaliga Server grupper från Azure Portal.
- Om du använder NFS måste du för närvarande ange allowRunAsRoot till true i din distributions profil fil innan du skapar data styrenheten för Azure-bågen.
- Endast SQL-och PostgreSQL-inloggning.  Inget stöd för Azure Active Directory eller Active Directory.
- Att skapa en datakontrollant i OpenShift kräver avslappnad säkerhets begränsningar.  I dokumentationen finns mer information.
- Det går inte att skala antalet postgres för storskaliga _arbets noder._
- Om du använder Azure Kubernetes service Engine (AKS Engine) på Azure Stack hubb med data styrenheten för Azure Arc och databas instanser, stöds inte uppgradering till en nyare Kubernetes-version. Avinstallera Azure Arc data Controller och alla databas instanser innan du uppgraderar Kubernetes-klustret.
- För hands versionen stöder inte säkerhets kopiering/återställning för postgres version 11-motorn. Den stöder bara säkerhets kopiering/återställning för postgres version 12.
