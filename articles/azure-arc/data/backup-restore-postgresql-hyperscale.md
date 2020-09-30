---
title: Säkerhetskopiering och återställning för Azure Database for PostgreSQL Hyperskala-servergrupper
description: Säkerhetskopiering och återställning för Azure Database for PostgreSQL Hyperskala-servergrupper
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: dde4db7f3eb476b7645e910504e48fea8bb6df0c
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569708"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Säkerhets kopiering och återställning av Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper

Du kan göra en fullständig säkerhets kopiering/återställning av din Azure Arc-aktiverade PostgreSQL-Server grupp. När du gör det säkerhets kopie ras hela uppsättningen databaser på alla noder i din Azure Arc-aktiverade PostgreSQL-Server grupp, och/eller återställs.
Om du vill göra en säkerhets kopia och återställa den måste du kontrol lera att en lagrings klass för säkerhets kopiering har kon figurer ATS för Server gruppen. För tillfället måste du ange en lagrings klass för säkerhets kopiering vid den tidpunkt då du skapar Server gruppen. Det går inte att konfigurera server gruppen för att använda en lagrings klass för säkerhets kopiering när den har skapats.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> För hands versionen stöder inte säkerhets kopiering/återställning för version 11 av postgres-motorn. Den stöder bara säkerhets kopiering/återställning för postgres version 12.

## <a name="verify-configuration"></a>Verifiera konfigurationen

Kontrol lera först att den befintliga Server gruppen har kon figurer ATS för att använda säkerhets kopierings lagrings klass.

Kör följande kommando när du har angett namnet på Server gruppen:
```console
 azdata arc postgres server show -n postgres01
```
Titta på lagrings avsnittet i utdata:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Om du ser namnet på en lagrings klass som anges i avsnittet "säkerhets kopieringar" i kommandots utdata, betyder det att din server grupp har kon figurer ATS för att använda en lagrings klass för säkerhets kopiering och är redo för att du ska kunna säkerhetskopiera och återställa. Om du inte ser avsnittet "säkerhets kopiering" måste du ta bort och återskapa Server gruppen för att konfigurera säkerhets kopierings lagrings klass. I det här läget är det ännu inte möjligt att konfigurera en lagrings klass för säkerhets kopiering när Server gruppen har skapats.

>[!IMPORTANT]
>Om din server grupp redan har kon figurer ATS för att använda en lagrings klass för säkerhets kopiering hoppar du över nästa steg och går direkt till steg "gör en manuell fullständig säkerhets kopiering".

## <a name="create-a-server-group"></a>Skapa en Server grupp 

Skapa sedan en Server grupp som kon figurer ATS för säkerhets kopiering/återställning.

För att kunna göra säkerhets kopior och återställa dem måste du skapa en server som är konfigurerad med en lagrings klass.

Kör följande kommando för att hämta en lista över tillgängliga lagrings klasser i Kubernetes-klustret:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Om du till exempel har skapat en enkel miljö baserat på kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Gör manuell fullständig säkerhets kopiering

Ta sedan en manuell fullständig säkerhets kopiering.

Kör följande kommando för att göra en fullständig säkerhets kopia av hela data-och loggmappen i Server gruppen:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Där:
- __namn__ anger namnet på en säkerhets kopia
- __Server namn__ anger en Server grupp
- __no-wait__ anger att kommando raden inte väntar på att säkerhets kopieringen ska slutföras för att du ska kunna fortsätta att använda detta kommando rads fönster

>**Obs!** kommandot som gör att du kan visa en lista över säkerhets kopior som är tillgängliga för återställning visar inte än, datum/tid då säkerhets kopian gjordes. Vi rekommenderar att du ger ett namn till säkerhets kopian (med parametern-name) som innehåller information om datum/tid.

Det här kommandot koordinerar en distribuerad fullständig säkerhets kopiering över alla noder som utgör din Azure Arc-aktiverade PostgreSQL-Server grupp. Med andra ord säkerhets kopie ras alla data i koordinator-och arbetsnoderna.

Exempel:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

När säkerhets kopieringen har slutförts returneras ID, namn och tillstånd för säkerhets kopian. Exempel:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Det är ännu inte möjligt att:
> - Schemalägga automatiska säkerhets kopieringar
> - Visa förloppet för en säkerhets kopia medan den tas

## <a name="list-backups"></a>Visa säkerhetskopior

Lista de säkerhets kopior som är tillgängliga för återställning.

Om du vill visa en lista över säkerhets kopior som är tillgängliga för återställning kör du följande kommando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Exempel:
```console
azdata arc postgres backup list --server-name postgres01
```

Den returnerar utdata som:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Timestamp anger tidpunkten i UTC då säkerhets kopieringen gjordes.

## <a name="restore-a-backup"></a>Återställ en säkerhetskopia

Om du vill återställa säkerhets kopian av en hel Server grupp kör du kommandot:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Där:
- __säkerhets kopierings-ID__ är ID: t för säkerhets kopian som visas i listan med säkerhets kopierings kommandot (se steg 3).
Detta koordinerar en distribuerad fullständig återställning över alla noder som utgör din Azure Arc-aktiverade PostgreSQL-Server grupp. Med andra ord återställs alla data i koordinator-och arbetsnoderna.

Exempel:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

När återställningen är klar returnerar den utdata som detta till kommando raden:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Det är ännu inte möjligt att:
> - Återställa en säkerhets kopia genom att ange dess namn
> - Återställa en Server grupp under ett annat namn eller i en annan server grupp
> - Visa förloppet för en återställnings åtgärd

## <a name="delete-backups"></a>Ta bort säkerhetskopior
Kvarhållning av säkerhets kopior kan inte anges som för hands version. Du kan dock manuellt ta bort säkerhets kopior som du inte behöver.
Det allmänna kommandot för att ta bort säkerhets kopior är:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
där:
- `--server-name` är namnet på den server grupp från vilken användaren vill ta bort en säkerhets kopia
- `--name` är namnet på den säkerhets kopia som ska tas bort
- `-id`är ID: t för den säkerhets kopia som ska tas bort

> [!NOTE]
> `--name` och `-id` är ömsesidigt uteslutande.

Du kan hämta namnet och ID: t för dina säkerhets kopieringar genom att köra säkerhets kopierings kommandot enligt beskrivningen i föregående stycke.

Anta till exempel att du har följande säkerhets kopior i listan:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
och du vill ta bort den första av dem, kör du följande kommando:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Om du skulle se en lista över säkerhets kopiorna i det här läget skulle du få följande utdata:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Om du vill ha mer information om kommandot Ta bort kör du:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att skala ut (lägga till arbetsnoder)](scale-out-postgresql-hyperscale-server-group.md) i Server gruppen
- Läs mer om att [skala upp eller ned (öka/minska minnes-och virtuella kärnor)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) i Server gruppen
