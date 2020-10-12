---
title: Skapa datakontrollant i Azure Data Studio
description: Skapa datakontrollant i Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f6fd8169c587e928da9946d74335ddc758889144
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273151"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Skapa datakontrollant i Azure Data Studio

Du kan skapa en datakontrollant med Azure Data Studio via distributions guiden och antecknings böckerna.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Förutsättningar

- Du behöver åtkomst till ett Kubernetes-kluster och har kubeconfig-filen konfigurerad så att den pekar på det Kubernetes-kluster som du vill distribuera till.
- Du måste [Installera klient verktygen](install-client-tools.md) , inklusive **Azure Data Studio** Azure Data Studio tillägg som kallas **Azure Arc** och **Azure Data CLI**.
- Du måste logga in på Azure i Azure Data Studio.  Gör så här: Skriv CTRL/Command + SHIFT + P för att öppna fönstret kommando text och skriv **Azure**.  Välj **Azure: Logga**in.   I panelen, som visas klickar du på +-ikonen längst upp till höger för att lägga till ett Azure-konto.

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>Använd distributions guiden för att skapa data styrenheten för Azure Arc

Följ dessa steg om du vill skapa en Azure Arc-dataenhet med hjälp av distributions guiden.

1. I Azure Data Studio klickar du på fliken anslutningar i det vänstra navigerings fältet.
2. Klicka på knappen **...** överst på panelen anslutningar och välj **ny distribution...**
3. I guiden Ny distribution väljer du **Azure Arc data Controller**, markerar kryss rutan licens godkännande och klickar sedan på knappen **Välj** längst ned.
4. Använd standard filen kubeconfig eller Välj en annan.  Klicka på **Nästa**.
5. Välj en Kubernetes-kluster kontext. Klicka på **Nästa**.
6. Välj en fil för distributions konfigurations profilen beroende på ditt mål Kubernetes-kluster. **Klicka på nästa**.
8. Välj önskad prenumeration och resurs grupp.
9. Ange ett namn för data styrenheten och för det namn område som data styrenheten ska skapas i.  

> [!NOTE]
> Om namn området redan finns kommer det att användas om namn området inte redan innehåller andra Kubernetes-objekt – poddar osv.  Om namn området inte finns skapas ett försök att skapa namn området.  Att skapa ett namn område i ett Kubernetes-kluster kräver administratörs behörighet för Kubernetes-kluster.  Om du inte har administratörs behörighet för Kubernetes kan du be din Kubernetes-kluster administratör att utföra de första stegen i artikeln [skapa en datakontrollant med Kubernetes-Native-verktyg](./create-data-controller-using-kubernetes-native-tools.md) som måste utföras av en Kubernetes-administratör innan du slutför den här guiden.

> [!NOTE]
> Obs! data styrenheten och namn områdets namn kommer att användas för att skapa en anpassad resurs i Kubernetes-klustret så att de måste följa [namn konventionerna för Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names).

10. Välj en Azure-plats.
   
> [!NOTE]
> Azure-platsen som väljs här är den plats i Azure där *metadata* om data styrenheten och databas instanserna som den hanterar kommer att lagras.  Data styrenheten och databas instanserna kommer att faktiskt crewted i ditt Kubernetes-kluster, oavsett var de befinner sig.

11.  Ange ett användar namn och lösen ord och bekräfta lösen ordet för användar kontot för data styrenhetens administratör.

> [!NOTE]
> Lösen ordet måste vara minst 8 tecken långt.

1.  Klicka på **Nästa**.
2.  Granska och klicka på **skript till Notebook**.
3.  **Granska den genererade antecknings boken**.  Gör nödvändiga ändringar, till exempel lagrings klass namn eller tjänst typer.
4.  Klicka på **Kör allt** längst upp i antecknings boken.

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet "Arc".  Om du har använt ett annat namn på namn område/data enhet kan du ersätta "Arc" med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).
