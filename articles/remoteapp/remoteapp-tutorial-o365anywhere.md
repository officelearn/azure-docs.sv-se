<properties
   pageTitle="Få samma Office 365-upplevelse på alla enheter med Azure RemoteApp | Microsoft Azure"
   description="Lär dig att dela en Office 365-app med dina användare genom att använda Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="04/05/2016"
   ms.author="guscatal;elizapo"/>


# Få samma Office 365-upplevelse på alla enheter med Azure RemoteApp

Den här artikeln beskriver hur du distribuerar Office 365 på alla enheter i företaget. Användarna kan få samma funktioner och användargränssnitt på Android, Apple och Windows.

Vi gör det genom att använda Azure RemoteApp och låta Office 365 vara värd för skalningsbara virtuella datorer i Azure som användarna kan ansluta till. Den här uppsättningen av virtuella datorer kallar vi ”molnsamlingen”.

## Skapa en molnsamling

Först när du har skapat ett Azure-konto navigerar du till **RemoteApp** genom att klicka på länken till vänster.
![Visa Azure RemoteApp på Azure-portalen](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Fortsätt sedan genom att klicka på **ny** längst ned och ”snabbt skapa” en samling. Ange de uppgifter som vi tillhandahåller om namn, region, prenumeration och plan samt bilden ”Office Proffesional 2013”.
![Dialogrutan Skapa](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

När du har gjort klart formuläret ska samlingen börja skapas. Det kan ta upp till ungefär en timme.

![Väntar](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

När processen är klar ser det ut ungefär så här. Om vi klickar på **Publicera** kan vi se att de flesta Office-program redan har publicerats för oss.
![Samlingen har skapats](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Publicerade appar](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Vid det här laget kan du också lägga till fler användare som har åtkomst till den här samlingen genom att klicka på **Användaråtkomst**.
![Konfigurera användaråtkomst](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Nu ska vi prova att ansluta till Office 365!

## Ansluta till Office 365

Vi ska gå till [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), rulla ned och klicka på **Ladda ned klienter** och installera Azure RemoteApp-klienten på den enheten du använder. Skärmbilderna nedan gäller för Windows.

När programmet startas blir du ombedd att logga in med ditt Microsoft-konto (kallades förut ”Live ID”). Använd samma som ditt Azure-konto för tillfället. När du är inloggad bör du se ett meddelande om nya inbjudningar. Om du klickar på det ska du se en lista som liknar den nedan. Acceptera den inbjudan som matchar din mejl som Azure-kontoägare.

![Ny inbjudan](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Hur det ser ut när det finns nya inbjudningar.

![Godkänna ett program](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Du bör se alla Office-program i Azure RemoteApp-klienten när du har accepterat inbjudan.

![Lista över appar](./media/remoteapp-tutorial-o365anywhere/9-work.png)

När du klickar på någon av dessa bör programmet startas på den virtuella Azure-datorn och du är klar! Ha det så kul!

![startar](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)



<!--HONumber=Jun16_HO2-->


