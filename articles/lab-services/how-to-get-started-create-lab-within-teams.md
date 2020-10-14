---
title: Kom igång och skapa ett Azure Lab Services labb från team
description: Lär dig hur du kommer igång och skapar ett Azure Lab Services labb från team.
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 0604e2934ff6b011acfa9dd4a4b25fa58193e69b
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044453"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>Kom igång och skapa ett labb tjänst labb från team

Den här artikeln visar hur du lägger till **Azure Lab Services** -appen i ett team och hur du skapar ett labb i MS Teams-miljön.

## <a name="prerequisites"></a>Krav

I den här självstudien skapar du ett labb med virtuella datorer för ditt team. Om du vill konfigurera ett labb i ett labb konto måste du vara medlem i någon av de här rollerna i labb kontot: ägare, labb skapare eller deltagare. Det konto som du använde för att skapa ett labbkonto läggs automatiskt till i ägarrollen. Så du kan använda det användar konto som du använde för att skapa ett labb konto för att skapa ett labb.

Här är det vanligaste arbets flödet när du använder Azure Lab Services i team

1. Användaren [skapar ett labb konto](tutorial-setup-lab-account.md#create-a-lab-account) på Azure Portal.
1. Ett [labb konto skapare lägger till andra användare](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role) i **labb skaparen** -rollen. Labb kontots skapare/administratör lägger till exempel till lärare till **labb skapare** rollen så att de kan skapa labb för sina klasser.
1. Sedan skapar läraren labbet, förkonfigurerar mallen VM och publicerar labbet för att etablera virtuella datorer till alla i teamet.
1. När labbet har publicerats tilldelas en virtuell dator till alla i listan Grupp medlemskap vid sin första inloggning till Azure Lab Services, antingen genom att klicka på fliken som innehåller **Azure Lab Services** app i Teams (SSO) eller genom att komma åt [labb webbplatsen](https://labs.azure.com). Användare kan sedan använda den virtuella datorn för att utföra klassen och läxor.

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>Lägg till Azure Lab Services app som en flik i ett team

Du, som en grupp ägare, kan lägga till **Azure Lab Services** app direkt i dina team kanaler, och sedan är appen tillgänglig för alla i teamet som ska användas. Följ de tre stegen nedan:

1. Navigera till den Team-kanal där du vill lägga till appen och välj **+** att lägga till en flik. 
1. Sök efter **Azure Lab Services** från fliken Alternativ och Lägg till den här appen. 

    > [!NOTE]
    > Endast grupp **ägare** kommer att kunna skapa labb för teamet.
1. Välj ett labb tjänst konto som du vill använda för att skapa klass rum labb i det här teamet. 

    Azure Lab Services använder enkel inloggning på [Azure Lab Services webbplats](https://labs.azure.com) och hämtar alla labb konton som du har åtkomst till. 

    De konton som finns i samma klient organisation som team och för vilka du har åtkomst till **ägare**, **deltagare**eller **skapare** visas. 

   ![Välkommen till sensorn](./media/integrate-with-teams/welcome.png) 
1. Tryck på **Spara** och fliken läggs till i kanalen.

    Nu kan du välja fliken **Azure Lab Services** från din kanal och börja hantera labb enligt beskrivningen i följande steg.

När labb kontot har valts kommer team ägare att kunna skapa labb för teamet. Hela processen för att skapa labb och alla aktiviteter på labb nivån kan utföras i team. Användare kan välja att skapa flera labb i samma team och teamets ägare, med lämplig åtkomst på labb konto nivån, ser bara de labb som är kopplade till det aktuella teamet.

## <a name="deleting-classroom-labs"></a>Ta bort klass rums labb

Ett labb som skapats i team kan tas bort på [webbplatsen labb tjänster](https://labs.azure.com) genom att ta bort labbet direkt, enligt beskrivningen i [Hantera klass rum labb i Azure Lab Services](how-to-manage-classroom-labs.md). 

Labb borttagning utlöses också när teamet tas bort. Om teamet som labbet skapas i tas bort, skulle labbet tas bort automatiskt 24 timmar efter att synkroniseringen av den automatiska användar listan har utlösts. 

Borttagning av fliken eller avinstallation av appen innebär inte att labbet tas bort. Om fliken tas bort kommer användare i listan över team medlemskap fortfarande att kunna komma åt de virtuella datorerna på [webbplatsen labb tjänster](https://labs.azure.com) , såvida inte borttagningen av labbet uttryckligen utlöses genom att du tar bort labbet på webbplatsen eller tar bort teamet. 

## <a name="next-steps"></a>Nästa steg

När ett labb skapas i team fylls labb användar listan i automatiskt och synkroniseras med team medlemskapet. Alla i teamet, inklusive ägare, medlemmar och gäster kommer automatiskt att läggas till i labb användar listan. Azure Lab Services kommer att underhålla en synkronisering med grupp medlemskapet och en automatisk synkronisering utlöses var 24: e timme. Mer information finns i:

[Hantera användar listor i labb tjänster från team](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>Se även

Se även följande artiklar:

- [Använda Azure Lab Services i team översikten](lab-services-within-teams-overview.md)
- [Hantera användar listor för labb i team](how-to-manage-user-lists-within-teams.md)
- [Hantera Labbets VM-pool i Teams](how-to-manage-vm-pool-within-teams.md)
- [Skapa och hantera labb scheman i team](how-to-create-schedules-within-teams.md)
- [Åtkomst till en virtuell dator i Teams – elev visning](how-to-access-vm-for-students-within-teams.md)

