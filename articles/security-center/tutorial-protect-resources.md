---
title: "Självstudiekurs för Azure Security Center – skydda dina resurser med Azure Security Center | Microsoft Docs"
description: "Den här kursen visar hur du konfigurerar bara komma åt princip och en princip för åtkomstkontroll i tid VM."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: f0a32f90e68101f805a52427fab2d5bb29b94939
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudier: Skydda dina resurser med Azure Security Center
Security Center begränsar din utsättas för hot med hjälp av kontroller åtkomst- och blockera skadlig aktivitet. Precis i tid virtuell dator (VM) minskar åtkomst din risken för attacker genom att aktivera du neka beständig åtkomst till virtuella datorer. I stället kan du ange kontrollerade och granskad åtkomst till virtuella datorer bara när de behövs. Anpassningsbar programkontroller hjälpa att skydda virtuella datorer mot skadlig kod genom att kontrollera vilka program kan köras på din virtuella dator. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera bara tidpunkt VM åtkomstprincip
> * Konfigurera en princip för åtkomstkontroll

Om du inte har en Azure-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
Du måste vara i Security Center Standard prisnivån för att gå igenom de funktioner som beskrivs i den här kursen. Du kan försöka Security Center Standard för de första 60 dagarna utan kostnad. Snabbstart [Onboard din Azure-prenumeration till Security Center Standard](security-center-get-started.md) vägleder dig igenom hur du uppgraderar till Standard.

## <a name="manage-vm-access"></a>Hantera VM-åtkomst
Precis i tid kan VM åtkomst användas för att låsa inkommande trafik till din virtuella Azure-datorer minskar risken för attacker och enkel åtkomst till att ansluta till virtuella datorer när de behövs.

Precis i tid är VM-åtkomst i förhandsgranskningen.

Hanteringsportar behöver inte vara öppen när som helst. De behöver bara vara öppen när du är ansluten till den virtuella datorn, till exempel för att utföra uppgifter för hantering och underhåll. När precis i tid är aktiverat, använder Security Center Nätverkssäkerhetsgrupp (NSG) regler som begränsar åtkomsten till hanteringsportar och de inte kan riktas av angripare.

1. På huvudmenyn Security Center, Välj **precis i tid VM access** under **DEFENSE ADVANCED molnet**.

  ![Just-in-time-åtkomst till virtuell dator][1]

  **Just-in-time-åtkomst för VM** innehåller information om tillståndet för dina virtuella datorer:

  - **Konfigurerad** -virtuella datorer som har konfigurerats för att stödja just-in-time VM-åtkomst.
  - **Rekommenderade** -virtuella datorer som stöder just-in-time-åtkomst för VM men inte har konfigurerats att.
  - **Ingen rekommendation** -orsaker till att en virtuell dator inte till rekommenderas är:

    - NSG - på samma sätt som saknas i tid lösning kräver en NSG ska vara på plats.
    - Klassiska VM - Security Center just-in-time-åtkomst för VM stöder för närvarande endast virtuella datorer som distribueras via Azure Resource Manager.
    - Andra - en virtuell dator finns i den här kategorin om den bara i tid lösningen har inaktiverats i säkerhetsprincipen för prenumerationen eller resursgruppen och att den virtuella datorn saknar en offentlig IP-adress och inte har en NSG på plats.

2. Välj en rekommenderad virtuell dator och klicka på **aktivera JIT på 1 VM** så här konfigurerar du bara i principen för tid för den virtuella datorn:

  Du kan spara standard portar som Security Center rekommenderar eller du kan lägga till och konfigurera en ny port som du vill aktivera den bara i Tidslösning. I den här kursen ska vi lägga till en port genom att välja **Lägg till**.

  ![Lägg till portinställningar][2]

3. Under **Lägg till Portkonfiguration**, identifierar du:

  - Porten
  - Vilken typ av protokoll
  - Tillåtna käll-IP-adresser - IP-adressintervall som tillåts för att få åtkomst vid ett godkänt förfrågan
  - Tid för maximal begäran - maximala tidsperioden som kan öppna en specifik port

4. Välj **OK** att spara.

## <a name="harden-vms-against-malware"></a>Skydda virtuella datorer mot skadlig kod
Anpassningsbar programkontroller hjälpa dig att definiera en uppsättning program som tillåts att köra på konfigurerade resursgrupper, vilket bland andra fördelar som hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs i den virtuella datorn och hjälper dig att tillämpa vitlisteregler med den här intelligensen.

Anpassningsbar programkontroller är i förhandsgranskningen. Den här funktionen är endast tillgängligt för Windows-datorer.

1. Gå tillbaka till huvudmenyn Security Center. Under **DEFENSE ADVANCED molnet**väljer **anpassningsbar programkontroller**.

   ![Anpassningsbara programkontroller][3]

  Den **resursgrupper** avsnittet innehåller tre flikar:

  - **Konfigurerad**: listan över resurs grupper som innehåller de virtuella datorerna som har konfigurerats med programkontrollen.
  - **Rekommenderade**: listan över resursgrupper för vilket program kontroll rekommenderas.
  - **Ingen rekommendation**: listan över resurs grupper med virtuella datorer utan några rekommendationer för kontroll av programmet. Till exempel virtuella datorer där program alltid byts ut och inte har uppnått ett stabilt tillstånd.

2. Välj den **rekommenderas** fliken för en lista över resursgrupper med rekommendationer för kontroll av programmet.

  ![Rekommendationer för kontroll av programmet][4]

3. Välj en resursgrupp att öppna den **skapa regler för åtkomstkontroll av programmet** alternativet. I **Välj virtuella datorer** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda programkontroll för. I **Välj processer för reglerna för lista över tillåtna** läser du listan med rekommenderade virtuella datorer och avmarkerar dem du inte vill använda. Listan innehåller:

  - **NAMNET**: fullständig programsökväg
  - **PROCESSER**: hur många program som finns på varje sökväg
  - **VANLIGA**: ”Ja” anger att dessa processer har körts på de flesta virtuella datorer i den här resursgruppen
  - **UTVINNINGSBARA**: en varningsikon anger om program som kan användas av en angripare för att kringgå vitlistning av program. Vi rekommenderar att du granskar programmen innan du godkänner dem.

4. När du är klar med dina val välja **skapa**.

## <a name="clean-up-resources"></a>Rensa resurser
Andra Snabbstart och självstudiekurser i den här samlingen bygger på denna Snabbstart. Om du vill fortsätta på att arbeta med efterföljande Snabbstart och självstudier fortsätta köras standardnivån och automatisk etablering aktiverad. Om du inte planerar att fortsätta eller om du vill gå tillbaka till den kostnadsfria nivån:

1. Återgå till huvudmenyn Security Center och välja **säkerhetsprincip**.
2. Välj den prenumeration eller princip som du vill gå tillbaka till ledig. **Säkerhetsprincip** öppnas.
3. Under **princip komponenter**väljer **prisnivå**.
4. Välj **lediga** ändra prenumerationen från standarden nivå till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill inaktivera automatisk etablering:

1. Återgå till huvudmenyn Security Center och välja **säkerhetsprincip**.
2. Välj den prenumeration som du vill inaktivera automatisk etablering.
3. Under **säkerhetsprincip – datainsamling**väljer **av** under **Onboarding** att inaktivera automatisk etablering.
4. Välj **Spara**.

>[!NOTE]
> Inaktivera automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Inaktivera automatisk etablering gränser säkerhetsövervakning för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen beskrivs hur du begränsar din utsättas för hot genom att:

> [!div class="checklist"]
> * Konfigurera bara i tid VM access princip för att tillhandahålla kontrolleras och granskas åtkomst till virtuella datorer bara vid behov
> * Konfigurera en anpassad princip för kontroller att styra vilka program kan köras på din virtuella dator

Gå vidare till nästa kurs att lära dig om att svara på säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudier: Svara på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
