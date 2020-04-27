---
title: Kör Micro Focus Enterprise Server 4,0 i en Docker-behållare på Azure Virtual Machines
description: Revara värd för dina IBM z/OS-arbetsbelastningar genom att köra Micro Focus Enterprise Server i en Docker-behållare på Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488486"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Kör Micro Focus Enterprise Server 4,0 i en Docker-behållare på Azure

Du kan köra Micro Focus Enterprise Server 4,0 i en Docker-behållare på Azure. Den här själv studie kursen visar hur du gör. Den använder Windows-CICS (Customer information Control system) acctdemo demonstration for Enterprise Server.

Docker lägger till portabilitet och isolering för program. Du kan till exempel exportera en Docker-avbildning från en virtuell Windows-dator som körs på en annan eller från en lagrings plats till en Windows-Server med Docker. Docker-avbildningen körs på den nya platsen med samma konfiguration – utan att du behöver installera Enterprise Server. Den är en del av bilden. Licens överväganden gäller fortfarande.

Den här självstudien installerar **Windows 2016-datacenter med behållare** virtuell dator (VM) från Azure Marketplace. Den här virtuella datorn innehåller **Docker 18.09.0**. Stegen nedan visar hur du distribuerar behållaren, kör den och sedan ansluter till den med en 3270-emulator.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du gå igenom följande krav:

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Micro Focus-programvaran och en giltig licens (eller prov licens). Om du är en befintlig Micro Focus-kund kontaktar du din Micro Focus-representant. Annars kan du [begära en utvärderings version](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Docker-demonstrations filerna ingår i Enterprise Server 4,0. I den här självstudien\_används\_en överordnad\_Server\_dockerfiles 4,0 Windows. zip. Kom åt den från samma plats som du har åtkomst till företags serverns installations fil eller gå till *Micro Focus* för att komma igång.

- Dokumentationen för [Enterprise Server och Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Skydda mediet från\_överordnad\_Server\_dockerfiles\_4,0 Windows. zip-fil. Säkra licens filen ES-Docker-Prod-XXXXXXXX. mflic (krävs för att bygga Docker-avbildningarna).

2. Skapa den virtuella datorn. Det gör du genom att öppna Azure Portal, välja **skapa en resurs** längst upp till vänster och filtrera efter *Windows Server*. I resultaten väljer du **Windows Server 2016 Data Center – med behållare**.

     ![Azure Portal Sök Resultat](media/01-portal.png)

3. Om du vill konfigurera egenskaperna för den virtuella datorn väljer du instans information:

    1. Välj storlek på den virtuella datorn. I den här självstudien bör du överväga att använda en **vanlig virtuell DS2\_v2** -dator med 2 virtuella processorer och 7 GB minne.

    2. Välj den **region** och **resurs grupp** som du vill distribuera till.

    3. Använd standardinställningen för **tillgänglighets alternativ**.

    4. För **användar namn**anger du det administratörs konto som du vill använda och lösen ordet.

    5. Kontrol lera att **port 3389 RDP** är öppen. Endast den här porten måste vara offentligt exponerad, så du kan logga in på den virtuella datorn. Godkänn sedan alla standardvärden och klicka på **Granska + skapa**.

     ![Fönstret Skapa en virtuell dator](media/container-02.png)

4. Vänta tills distributionen har slutförts (ett par minuter). Ett meddelande anger att den virtuella datorn har skapats.

5. Klicka på **gå till resurs** för att gå till **översikts** BLADet för den virtuella datorn.

6. Klicka på knappen **Anslut** till höger. Alternativen **Anslut till virtuell dator** visas till höger.

7. Klicka på knappen **Ladda ned RDP-fil** för att ladda ned RDP-filen som gör att du kan ansluta till den virtuella datorn.

8. När filen har hämtats öppnar du den och anger det användar namn och lösen ord som du skapade för den virtuella datorn.

     > [!NOTE]
     > Använd inte företagets autentiseringsuppgifter för att logga in. (RDP-klienten förutsätter att du kanske vill använda dessa. Du inte gör det.)

9.  Välj **fler alternativ**och välj sedan dina autentiseringsuppgifter för virtuella datorer.

Den virtuella datorn körs i det här läget via RDP. Du är inloggad och redo för nästa steg.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Skapa en sandbox-katalog och ladda upp zip-filen

1.  Skapa en katalog på den virtuella datorn där du kan ladda upp demonstrationen och licensens filer. Till exempel **C:\\sandbox**.

2.  Överför överordnad **\_Server\_dockerfiles\_4,0\_Windows. zip** och **es-Docker-Prod-xxxxxxxx. mflic-** filen till den katalog som du skapade.

3.  Extrahera innehållet i zip-filen till den överordnande **\_\_servern\_dockerfiles\_4,0 Windows** -katalogen som skapats av extraherings processen. Den här katalogen innehåller en README-fil (som HTML-och. txt-fil) och två under kataloger, **EnterpriseServer** och **exempel**.

4.  Kopiera **es – Docker-Prod-xxxxxxxx. mflic** till mappen C:\\sandbox\\-\_överordnad\_Server\\\\\_dockerfiles\_4,0 Windows EnterpriseServer och\\C\_:\_sandbox\_-\_Server\\dockerfiles\\4,0 Windows-exempel CICS kataloger.

> [!NOTE]
> Se till att du kopierar licens filen till båda katalogerna. De krävs för Docker build-steget för att se till att avbildningarna är korrekt licensierade.

## <a name="check-docker-version-and-create-base-image"></a>Kontrol lera Docker-versionen och skapa en bas avbildning

> [!IMPORTANT]
> Att skapa rätt Docker-avbildning är en två stegs process. Skapa först Enterprise Server 4,0-bas avbildningen.Skapa sedan en annan avbildning för x64-plattformen. Även om du kan skapa en x86-avbildning (32-bitars) använder du 64-bitars avbildningen.

1. Öppna en kommandotolk.

2. Kontrol lera att Docker är installerat. Skriv följande i kommandotolken:

    ```
        docker version
    ```

     Till exempel var versionen 18.09.0 när den skrevs.

3. Om du vill ändra katalogen skriver du **CD \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. Skriv **bld. bat IacceptEULA** för att börja bygga processen för den inledande bas avbildningen. Vänta några minuter för den här processen att köras. Observera i resultaten de två avbildningar som har skapats – en för x64 och en för x86:

     ![Fönstret Kommando visar bilder](media/container-04.png)

5. Om du vill skapa den slutliga avbildningen för CICS-demonstrationen växlar du till CICS-katalogen genom att skriva **\\CD\_sandbox\\\\\\\_Server\_dockerfiles\_4,0 Windows-exempel CICS**.

6. Skapa avbildningen genom att skriva **bld. bat x64**. Vänta några minuter på att processen ska köras och meddelandet som anger att avbildningen har skapats.

7. Skriv **Docker-avbildningar** för att visa en lista över alla Docker-avbildningar som är installerade på den virtuella datorn. Se till att **mikrofocus/es-acctdemo** är en av dem.

     ![Fönstret Kommando som visar es-acctdemo-avbildning](media/container-05.png)

## <a name="run-the-image"></a>Kör avbildningen 

1.  Starta Enterprise Server 4,0 och acctdemo-programmet genom att skriva följande i kommando tolken:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installera en 3270-termin Ale mula Tor till exempel [x3270](http://x3270.bgp.nu/) och Använd den för att ansluta via port 9040 till den avbildning som körs.

3.  Hämta IP-adressen för acctdemo-behållaren så att Docker kan fungera som en DHCP-server för de behållare som den hanterar:

    1.  Hämta ID: t för den behållare som körs. Skriv **Docker PS** i kommando tolken och anteckna ID (**22a0fe3159d0** i det här exemplet). Spara den för nästa steg.

    2.  Om du vill hämta IP-adressen för acctdemo-behållaren använder du behållar-ID: t från föregående steg enligt följande:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Ett exempel:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Anteckna IP-adressen för acctdemo-avbildningen. Till exempel är adressen i följande utdata 172.19.202.52.

     ![Fönstret Kommando visar IP-adress](media/container-06.png)

5. Montera avbildningen med emulatorn. Konfigurera emulatorn för att använda adressen för acctdemo-avbildningen och port 9040. Här är det **172.19.202.52:9040**. Ditt är liknande. Skärmen **inloggning till CICS** öppnas.

    ![Inloggning till CICS-skärmen](media/container-07.png)

6. Logga in i CICS-regionen genom att ange **SYSAD** för **användar-ID** och **SYSAD** för **lösen ordet**.

7. Ta bort skärmen med emulatorns Keymap. För x3270 väljer du meny alternativet **Keymap** .

8. Starta acctdemo-programmet genom att skriva **acct**. Den första skärmen för programmet visas.

     ![Skärm för konto demonstration](media/container-08.png)

9. Experimentera med konto typer för visning. Skriv till exempel **D** för begäran och **11111** för **kontot**. Andra konto nummer att testa är 22222, 33333 och så vidare.

     ![Skärm för konto demonstration](media/container-09.png)

10. Om du vill visa administrations konsolen för företags servern går du till kommando tolken och skriver **Start http: 172.19.202.52:86**

     ![Administrations konsol för Enterprise Server](media/container-010.png)

Klart! Nu kör vi och hanterar ett CICS-program i en Docker-behållare.

## <a name="next-steps"></a>Nästa steg

- [Installera Micro Focus Enterprise Server 4,0 och Enterprise Developer 4,0 på Azure](./set-up-micro-focus-azure.md)
- [Migrering av program från stordatorer](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
