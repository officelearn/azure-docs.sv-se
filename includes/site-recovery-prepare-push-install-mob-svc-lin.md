---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019264"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Förbereda en push-installation på en Linux-server

1. Kontrollera att det finns nätverksanslutning mellan Linux-datorn och processervern.
1. Skapa ett konto som processervern kan använda för att komma åt datorn. Kontot måste vara en **rotanvändare** på Linux-källservern. Använd det här kontot bara för push-installation och för uppdateringar.
1. Kontrollera att /etc/hosts-filen på Linux-källservern innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.
1. Installera de senaste openssh-, openssh-server- och openssl-paketen på den dator som du vill replikera.
1. Kontrollera att Secure Shell (SSH) är aktiverat och körs på port 22.
1. Aktivera SFTP undersystemet och lösenordsautentisering i sshd_config-filen. Följ de här stegen:

    a. Logga in som **rot**.

    b. I den **/etc/ssh/sshd_config** filen, leta reda på raden som börjar med **PasswordAuthentication**.

    c. Ta bort raden och ändra värdet till **Ja**.

    d. Hitta raden som börjar med **undersystem**, och ta bort den.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Starta om **sshd**-tjänsten.

1. Lägg till kontot som du skapat i CSPSConfigtool. Följ de här stegen:

    a. Logga in på konfigurationsservern.

    b. Öppna **cspsconfigtool.exe**. Det är tillgängligt som en genväg på skrivbordet och i mappen %ProgramData%\home\svsystems\bin.

    c. På den **hantera konton** fliken **Lägg till konto**.

    d. Lägg till kontot som du skapade.

    d. Ange autentiseringsuppgifterna som du använder när du aktiverar replikering för en dator.
