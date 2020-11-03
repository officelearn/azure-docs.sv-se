---
title: Azure Arc-aktiverade data tjänster – viktig information
description: Senaste versions information
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 10/29/2020
ms.topic: conceptual
ms.openlocfilehash: e7312ffd4d55f0403359f8aad2d0a8433a716f77
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280377"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Viktig information – Azure Arc-aktiverade data tjänster (för hands version)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="october-2020"></a>Oktober 2020 

### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

I den här versionen införs följande ändringar: 

* PostgreSQL-filer för anpassade resurs definitioner (CRD) ersätter termen `shards` med ett nytt namn `workers` . Den här termen ( `workers` ) matchar kommando rads parameterns namn.

* `azdata arc postgres server delete` du uppmanas att bekräfta innan du tar bort en postgres-instans.  Använd `--force` för att hoppa över prompten.

### <a name="additional-changes"></a>Ytterligare ändringar

* En ny valfri parameter har lagts till i `azdata arc postgres server create` anropad `--volume-claim mounts` . Värdet är en kommaavgränsad lista över volym anspråks monteringar. En volym anspråks montering är ett par av volym typ och PVC-namn. Volym typen för tillåter nu bara `backup` .  I PostgreSQL, när volym typen är `backup` , monteras PVC: n på `/mnt/db-backups` .  Detta möjliggör delning av säkerhets kopior mellan postgres-instanser så att säkerhets kopian av en postgres-instans kan återställas i en annan.

* Ett nytt kort namn för PostgresSQL anpassade resurs definitioner: 

  * `pg11` 

  * `pg12`

* Telemetri-uppladdning genom att förse användaren med antingen:

   * Antal poäng som laddats upp till Azure

     eller 

   * Om inga data har lästs in i Azure kan du prova att försöka igen.

* `azdata arc dc debug copy-logs` läser nu även från `/var/opt/controller/log` mapp och samlar in postgres-loggar.

*   Visa en fungerande indikator under postgres skapa och återställa säkerhets kopiering.

* `azdata arc postrgres backup list` innehåller nu information om säkerhets kopierings storlek.

* Egenskapen administratörs namn för SQL-hanterad instans har lagts till i den högra kolumnen i översikts bladet i Azure Portal.



## <a name="september-2020"></a>September 2020

Azure Arc-aktiverade data tjänster har publicerats för offentlig för hands version. Med ARC-aktiverade data tjänster kan du hantera data tjänster var som helst.

- SQL-hanterad instans
- PostgreSQL-storskalig

Mer information finns i [Vad är Azure Arc-aktiverade data tjänster?](overview.md)

## <a name="known-limitations-and-issues"></a>Kända begränsningar och problem

- SQL-hanterade instans namn får inte vara längre än 13 tecken
- Ingen uppgradering på plats för Azure Arc-datakontrollanten eller databas instanserna.
- De Arc-aktiverade datatjänstcontaineravbildningarna är inte signerade.  Du kan behöva konfigurera dina Kubernetes-noder så att de tillåter hämtning av osignerade containeravbildningar.  Om du till exempel använder Docker som container runtime, kan du ange miljövariabeln DOCKER_CONTENT_TRUST = 0 och starta om.  Andra containerkörningar har liknande alternativ, som i [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Det går inte att skapa Azure Arc-aktiverade SQL-hanterade instanser eller PostgreSQL för storskaliga Server grupper från Azure Portal.
- Om du använder NFS måste du ange `allowRunAsRoot` till `true` i din distributions profil fil innan du skapar data styrenheten för Azure-bågen.
- Endast SQL-och PostgreSQL-inloggning.  Inget stöd för Azure Active Directory eller Active Directory.
- Att skapa en datakontrollant i OpenShift kräver avslappnad säkerhets begränsningar.  I dokumentationen finns mer information.
- Det går inte att skala antalet PostgresSQL för storskaliga _arbets noder._
- Om du använder Azure Kubernetes service Engine (AKS Engine) på Azure Stack hubb med data styrenheten för Azure Arc och databas instanser, stöds inte uppgradering till en nyare Kubernetes-version. Avinstallera Azure Arc data Controller och alla databas instanser innan du uppgraderar Kubernetes-klustret.
- För hands versionen stöder inte säkerhets kopiering/återställning för postgres version 11-motorn. Den stöder bara säkerhets kopiering/återställning för postgres version 12.
- Azure Kubernetes service (AKS), kluster som sträcker sig över [flera tillgänglighets zoner](../../aks/availability-zones.md) stöds för närvarande inte för Azure Arc-aktiverade data tjänster. Du undviker det här problemet genom att ta bort alla zoner från urvals kontrollen när du skapar AKS-klustret i Azure Portal, om du väljer en region där zoner är tillgängliga. Se följande bild:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Avmarkera kryss rutorna för varje zon för att ange ingen.":::

## <a name="next-steps"></a>Nästa steg
  
> **Vill du bara testa saker?**  
> Kom igång snabbt med [Azure Arc-rivstart med](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator.

[Installera klient verktyg](install-client-tools.md)

[Skapa data styrenheten för Azure-bågen](create-data-controller.md) (kräver att klient verktygen installeras först)

[Skapa en Azure SQL-hanterad instans på Azure-bågen](create-sql-managed-instance.md) (du måste först skapa en Azure båg-datakontrollant)

[Skapa en Azure Database for PostgreSQL skalnings Server grupp på Azure-bågen](create-postgresql-hyperscale-server-group.md) (du måste först skapa en Azure-båge data Controller)
