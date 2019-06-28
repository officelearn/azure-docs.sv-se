---
title: Skapa Azure VMware-lösning av CloudSimple privat moln
description: Beskriver hur du skapar ett CloudSimple privat moln för att utöka VMware-arbetsbelastningar till molnet med verksamheten flexibel och affärskontinuitet
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332627"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Skapa ett privat moln CloudSimple

Skapa ett privat moln hjälper dig att hantera olika vanliga behov för nätverksinfrastruktur:

* **Tillväxt**. Om du har nått en tidpunkt för uppdatering av maskinvara för din befintliga infrastruktur, kan du expandera med inga nya maskinvaruinvesteringar krävs ett privat moln.

* **Snabb expansion**. Om någon tillfälliga eller oplanerad kapacitet behöver uppstår, kan du skapa ytterligare kapacitet utan fördröjning i ett privat moln.

* **Ökat skydd**. Med ett privat moln med tre eller fler noder får du automatisk redundans och hög tillgänglighet skydd.

* **Långsiktig infrastrukturens behov**. Om dina Datacenter har nått sin kapacitet eller om du vill att omstrukturera för att sänka dina kostnader, kan du ta bort datacenter och migrera till en molnbaserad lösning samtidigt som kompatibel med enterprise-åtgärder i ett privat moln.

När du skapar ett privat moln kan få du ett enda vSphere-kluster och alla de virtuella hanteringsdatorerna som har skapats i klustret.

## <a name="before-you-begin"></a>Innan du börjar

Noder måste etableras innan du kan skapa ditt privata moln.  Mer information om etablering noder finns i [etablera noder för VMware-lösning genom CloudSimple - Azure](create-nodes.md) artikeln.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Få åtkomst till CloudSimple-portalen

Åtkomst till den [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Skapa ett nytt privat moln

1. På den **resurser** klickar du på **nya optimeras**.

    ![Skapa ett privat moln – hur du kommer igång](media/create-pc-button.png)

2. Välj platsen som värd för privata molnresurser.

3. Välj den CS28 eller CS36 noden typ you'ev etableras för det privata molnet. Det senare alternativet innehåller maximal kapacitet för beräkning och minne.

4. Välj antalet noder för det privata molnet. Du kan välja högst det tillgängliga antalet noder som you'ev [etablerade](create-nodes.md).

    ![Skapa ett privat moln – grundläggande inställningar](media/create-private-cloud-basic-info.png)

5. Klicka på **Nästa: Avancerade alternativ**.

6. Ange CIDR-intervall för undernät för virtuella vSphere/SAN-nätverket. Kontrollera att CIDR-intervall inte överlappar med något av dina lokala eller andra Azure-undernät (virtuella nätverk) eller med gateway-undernätet.  Använd inte någon CIDR-intervall som definieras på virtuella Azure-nätverk.
    
    **Alternativ för CIDR-intervall:** /24, /23, /22 eller /21. Ett/24 CIDR-intervall har stöd för upp till nio noder, en /23 CIDR-intervall som stöder upp till 41 noder och en /22 och /21 CIDR-intervall har stöd för upp till 64 noder (maximalt antal noder i ett privat moln).

    > [!CAUTION]
    > IP-adresser i vSphere/virtuellt SAN CIDR-intervall är reserverade för användning av privat moln-infrastruktur.  Använd inte IP-adressen i det här intervallet på någon virtuell dator.

7. Klicka på **Nästa: Granska och skapa**.

8. Granska inställningarna. Om du behöver ändra eventuella inställningar klickar du på **föregående**.

9. Klicka på **Skapa**.

Privat moln etablering startar när du klickar på Skapa.  Du kan övervaka förloppet från [uppgifter](https://docs.azure.cloudsimple.com/activity/#tasks) sidan på CloudSimple portal.  Etablering kan ta 30 minuter till två timmar.  Du får ett e-postmeddelande när etableringen har slutförts.

## <a name="next-steps"></a>Nästa steg

* [Expandera privat moln](expand-private-cloud.md)