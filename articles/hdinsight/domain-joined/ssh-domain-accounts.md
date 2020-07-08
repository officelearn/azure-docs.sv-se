---
title: Hantera SSH-åtkomst för domän konton i Azure HDInsight
description: Steg för att hantera SSH-åtkomst för Azure AD-konton i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 5be992ef8375f98b3c5978d8b71dc92ce9f91123
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86081510"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Hantera SSH-åtkomst för domän konton i Azure HDInsight

I säkra kluster tillåts som standard alla domän användare i [Azure AD DS](../../active-directory-domain-services/overview.md) till [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) till Head-och Edge-noderna. Dessa användare ingår inte i sudoers-gruppen och får inte rot åtkomst. Den SSH-användare som skapas när klustret skapas har rot åtkomst.

## <a name="manage-access"></a>Hantera åtkomst

Om du vill ändra SSH-åtkomst till vissa användare eller grupper uppdaterar du `/etc/ssh/sshd_config` på varje nod.

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öppna `ssh_confi` g-filen.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Ändra `sshd_config` filen efter behov. Om du begränsar användarna till vissa grupper kan inte de lokala kontona SSH-använda SSH till den noden. Följande är bara ett exempel på syntax:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Spara ändringarna: **CTRL + X**, **Y**, **RETUR**.

1. Starta om sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Upprepa stegen ovan för varje nod.

## <a name="ssh-authentication-log"></a>SSH-autentiseringsnyckel

SSH-autentiseringsprocessen skrivs in i `/var/log/auth.log` . Om du ser inloggnings fel via SSH för lokala konton eller domän konton måste du gå igenom loggen för att felsöka felen. Ofta kan problemet vara relaterat till vissa användar konton och det är vanligt vis en bra idé att testa andra användar konton eller SSH med hjälp av standard SSH-användaren (lokalt konto) och sedan försöka med en kinit.

## <a name="ssh-debug-log"></a>SSH fel söknings logg

Om du vill aktivera utförlig loggning måste du starta om `sshd` med `-d` alternativet. Precis som `/usr/sbin/sshd -d` du kan köra `sshd` på en anpassad port (till exempel 2222) så att du inte behöver stoppa huvud SSH-daemonen. Du kan också använda `-v` alternativet med SSH-klienten för att få fler loggar (klient sidans vy över felen).

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med Enterprise Security Package](./apache-domain-joined-manage.md)
* [Anslut till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
