---
title: Slutför en haveri beredskap för virtuella datorer
description: Den här artikeln visar hur du utför en haveri beredskap för virtuella datorer med hjälp av Azure VMware-lösningen
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 76a417b9ba00c4c0e6e958e5a04d19aecfe24563
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752264"
---
# <a name="complete-a-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Slutför en haveri beredskap för virtuella datorer med hjälp av Azure VMware-lösningen

Den här artikeln innehåller en process för att slutföra en haveri beredskap för dina virtuella datorer med VMware Hybrid Cloud Extensions-lösning (HCX) och med hjälp av ett privat moln i Azure VMware-lösningen som återställnings-eller mål plats.

VMware HCX tillhandahåller olika åtgärder som ger fin kontroll och granularitet i replikeringsprinciper. Tillgängliga åtgärder är:

- Back – när en katastrof har inträffat. Omvänd hjälper till att göra plats B till käll platsen och plats A där den skyddade virtuella datorn nu är i liv.

- Paus – pausa den aktuella replikeringsprincipen som är associerad med den virtuella datorn vald.

- Återuppta – pausa den aktuella replikeringsprincipen som är associerad med den valda virtuella datorn.

- Ta bort – ta bort den aktuella replikeringsprincipen som är associerad med den valda virtuella datorn.

- Synkronisera nu – slut på den virtuella datorn för den skyddade virtuella datorn.

I den här guiden omfattas följande scenarier för replikering:

- Skydda en virtuell dator eller en grupp med virtuella datorer.

- Slutför en test återställning av en virtuell dator eller en grupp med virtuella datorer.

- Återställa en virtuell dator eller en grupp med virtuella datorer.

- Omvänt skyddet av en virtuell dator eller en grupp med virtuella datorer.

## <a name="protect-virtual-machines"></a>Skydda virtuella datorer

Logga in på **vSphere-klienten** på käll platsen och få åtkomst till HCX-plugin- **programmet**.

:::image type="content" source="./media/disaster-recovery/hcx-vsphere.png" alt-text="Alternativet HCX i vSphere" border="true":::

Ange **Disaster Recovery** -sektionen och klicka på **skydda virtuella datorer**.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine.png" alt-text="Välj skydda virtuella datorer" border="true":::

I fönstret som öppnas väljer du källa och fjärranslutna platser, fjärrplatsen i det här fallet ska vara det privata molnet för Azure VMware-lösningen.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machines.png" alt-text="fönstret skydda virtuella datorer" border="true":::

Om det behövs väljer du standard alternativ för replikering:

- **Aktivera komprimering:** Rekommenderas för låga data flödes scenarier.

- **Aktivera Quiescence:** Pausar den virtuella datorn så att en konsekvent kopia synkroniseras till fjärrplatsen.

- **Mål lagring:** Välj fjärrdata lager för de skyddade virtuella datorerna. I ett privat moln i Azure VMware-lösningen ska det här valet vara virtuellt San data lager.

- **Beräknings behållare:** VSphere-klustret eller resurspoolen.

- **Målmapp:** Målmappen, den här inställningen är valfri och om ingen mapp har valts kommer de virtuella datorerna att gå förlorade direkt under det valda klustret.

- Återställnings punkt **:** Det här värdet är synkroniseringsfrekvensen mellan den virtuella käll datorn och den skyddade virtuella datorn och kan vara var som helst mellan 5 minuter och 24 timmar.

- **Intervall för ögonblicks bild:** Intervall mellan ögonblicks bilder.

- **Antal ögonblicks bilder:** Totalt antal ögonblicks bilder inom det konfigurerade intervallet för ögonblicks bilder.

:::image type="content" source="./media/disaster-recovery/protect-virtual-machine-options.png" alt-text="skydda alternativ för virtuella datorer" border="true":::

Välj en eller flera virtuella datorer i listan och konfigurera de replikeringsalternativ som krävs för den virtuella datorn.

Som standard ärver de virtuella datorerna den globala inställnings principen som kon figurer ATS i standardreplikerings alternativen. För varje nätverks gränssnitt i den valda virtuella datorn konfigurerar du den fjärranslutna **nätverks port gruppen** och väljer **Slutför** för att starta skydds processen.

:::image type="content" source="./media/disaster-recovery/network-interface-options.png" alt-text="alternativ för nätverks gränssnitt" border="true":::

Som du ser i följande bild kan du övervaka processen för var och en av de valda virtuella datorerna i samma katastrof återställnings plats.

:::image type="content" source="./media/disaster-recovery/protect-monitor-progress.png" alt-text="övervaka förloppet för skydd" border="true":::

När den virtuella datorn har skyddats kan du se de olika ögonblicks bilderna på fliken **ögonblicks bilder** .

:::image type="content" source="./media/disaster-recovery/list-of-snapshots.png" alt-text="lista över ögonblicks bilder" border="true":::

Den gula triangeln innebär att ögonblicks bilderna och det virtuella inte har testats i en test återställnings åtgärd.

Det finns viktiga skillnader mellan en virtuell dator som är avstängd och en som är påslagen.
Den föregående skärm bilden visar synkroniseringsprocessen för en ström på den virtuella datorn. Synkroniseringen startades tills den första ögonblicks bilden är klar, vilket är en fullständig kopia av den virtuella datorn och sedan slutförs nästa steg i det konfigurerade intervallet.

För en avstängd virtuell dator kommer den att synkroniseras med en kopia och sedan visas den virtuella datorn som inaktiv och skydds åtgärden visas som slutförd.

När den virtuella datorn är påslagen startar den synkroniseringen av processen på fjärrplatsen.

## <a name="complete-a-test-recover-of-virtual-machines"></a>Slutför en test återställning av virtuella datorer

Logga in på **vSphere-klienten** på fjärrplatsen, som är det privata molnet för Azure VMware-lösningen. I **HCX-plugin-programmet**i Disaster Recovery-avsnittet väljer du de lodräta ellipserna på valfri virtuell dator för att Visa menyn åtgärder. Välj **testa återställning av virtuell dator**.

:::image type="content" source="./media/disaster-recovery/test-recover-virtual-machine.png" alt-text="Välj Testa återställning av virtuell dator" border="true":::

I det nya fönstret väljer du alternativen för testet. Välj den ögonblicks bild som du vill använda för att testa olika tillstånd för den virtuella datorn.

:::image type="content" source="./media/disaster-recovery/choose-snapshot.png" alt-text="Välj en ögonblicks bild och klicka på testa" border="true":::

När du klickar på **test**startas återställnings åtgärden.

När test återställnings åtgärden har slutförts kan den nya virtuella datorn kontrol leras i Azure VMware-lösningen privat moln vCenter.

:::image type="content" source="./media/disaster-recovery/verify-test-recovery.png" alt-text="kontrol lera återställnings åtgärden" border="true":::

Slutligen när testningen har utförts på den virtuella datorn eller ett program som körs på den utför en rensning för att ta bort test instansen.

:::image type="content" source="./media/disaster-recovery/cleanup-test-instance.png" alt-text="Rensa test instans" border="true":::

## <a name="recover-virtual-machines"></a>Återställa virtuella datorer

Logga in på **vSphere-klienten** på fjärrplatsen, som är Azure VMware-lösningens privata moln, och få åtkomst till HCX-plugin- **programmet**.

För återställnings scenariot används en grupp virtuella datorer i det här exemplet.

Välj den virtuella dator som ska återställas i listan, öppna menyn **åtgärder** och välj **Återställ virtuella datorer**.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines.png" alt-text="återställa virtuella datorer" border="true":::

Konfigurera återställnings alternativen för varje instans och klicka på **Återställ** för att starta återställnings åtgärden.

:::image type="content" source="./media/disaster-recovery/recover-virtual-machines-confirm.png" alt-text="bekräfta återställning av virtuella datorer" border="true":::

När återställningen har slutförts visas de nya virtuella datorerna i den fjärranslutna vCenter Server inventeringen.

## <a name="complete-a-reverse-replication-on-virtual-machines"></a>Slutför en omvänd replikering på virtuella datorer

Logga in på **vSphere-klienten** i ditt privata moln i Azure VMware-lösningen och få åtkomst till HCX-plugin- **programmet**.
Det krävs att de ursprungliga virtuella datorerna på käll platsen är avstängda innan du startar den omvända replikeringen. Åtgärden kan inte utföras om de virtuella datorerna inte är avstängd.

Välj de virtuella datorer som ska replikeras tillbaka till käll platsen i listan, öppna menyn **åtgärder** och välj **Omvänd**. I popup-fönstret klickar du på **Omvänd** för att starta replikeringen.

:::image type="content" source="./media/disaster-recovery/reverse-operation-virtual-machines.png" alt-text="Välj Omvänd åtgärd under skydda åtgärder" border="true":::

Replikeringen kan övervakas i avsnittet information på varje virtuell dator.

:::image type="content" source="./media/disaster-recovery/review-reverse-operation.png" alt-text="granska resultatet av en omvänd åtgärd" border="true":::

## <a name="disaster-recovery-plan-automation"></a>Automatisering av katastrof återställnings plan

VMware HCX har för närvarande inte någon inbyggd mekanism för att skapa och automatisera en katastrof återställnings plan. Den här funktionen finns inte i HCX. Det innehåller dock en uppsättning REST API: er, inklusive API: er för haveri beredskap.

API-specifikationen kan nås i HCX Manager i URL: en.

Följande åtgärder i haveri beredskap omfattas av dessa API: er.

- Skydda

- Återställ

- Testa återställning

- Planerad återställning

- Reverse

- Söka i data

- Test rensning

- Pausa

- Återuppta

- Ta bort skydd

- Konfigurera om

Ett exempel på en nytto last för återställnings åtgärd i JSON visas nedan.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Med hjälp av dessa API: er kan kunden skapa en anpassad mekanism för att automatisera skapandet och körningen av en katastrof återställnings plan.
