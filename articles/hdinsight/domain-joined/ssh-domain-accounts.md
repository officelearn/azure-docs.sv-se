---
title: Hantera SSH-åtkomst för domänkonton i Azure HDInsight
description: Steg för att hantera SSH-åtkomst för Azure AD-konton i HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472523"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Hantera SSH-åtkomst för domänkonton i Azure HDInsight

På säkra kluster tillåts som standard alla domänanvändare i [Azure AD DS](../../active-directory-domain-services/overview.md) att [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) i huvud- och kantnoderna. Dessa användare är inte en del av sudoers-gruppen och får inte root-åtkomst. SSH-användaren som skapades när klustret skapades har root-åtkomst.

## <a name="manage-access"></a>Hantera åtkomst

Om du vill ändra SSH-åtkomst `/etc/ssh/sshd_config` till specifika användare eller grupper uppdaterar du på var och en av noderna.

1. Använd kommandot ssh för att ansluta till [klustret.](../hdinsight-hadoop-linux-use-ssh-unix.md) Redigera kommandot nedan genom att ersätta CLUSTERNAME med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Öppna `ssh_confi`filen g.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. Ändra `sshd_config` filen efter behov. Om du begränsar användare till vissa grupper kan inte de lokala kontona SSH till den noden. Följande är bara ett exempel på syntax:

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    Spara sedan ändringar: **Ctrl + X**, **Y**, **Enter**.

1. Starta om sshd.

    ```bash
    sudo systemctl restart sshd
    ```

1. Upprepa ovanstående steg för varje nod.

## <a name="ssh-authentication-log"></a>Logg för SSH-autentisering

SSH-autentiseringsloggen skrivs in i `/var/log/auth.log`. Om du ser några inloggningsfel via SSH för lokala konton eller domänkonton måste du gå igenom loggen för att felsöka felen. Ofta kan problemet vara relaterat till specifika användarkonton och det är oftast en bra idé att prova andra användarkonton eller SSH med standard-SSH-användaren (lokalt konto) och sedan försöka sig på en kinit.

## <a name="ssh-debug-log"></a>Felsöklogg för SSH

Om du vill aktivera detaljerad loggning `sshd` måste `-d` du starta om med alternativet. Som `/usr/sbin/sshd -d` du kan `sshd` också köra på en anpassad port (som 2222) så att du inte behöver stoppa de viktigaste SSH demon. Du kan `-v` också använda alternativet med SSH-klienten för att få fler loggar (klientsidan av felen).

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med Enterprise Security Package](./apache-domain-joined-manage.md)
* [Anslut till HDInsight (Apache Hadoop) med SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
