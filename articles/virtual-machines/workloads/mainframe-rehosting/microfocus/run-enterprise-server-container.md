---
title: Köra Micro fokus Enterprise Server 4.0 i en Docker-behållare på Azure Virtual Machines
description: Ange ny värd för dina IBM z/OS stordatorprogram arbetsbelastningar genom att köra Micro fokus Enterprise Server i en Docker-behållare på Azure Virtual Machines.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896602"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Kör Micro fokus Enterprise Server 4.0 i en Docker-behållare i Azure

Du kan köra Micro fokus Enterprise Server 4.0 i en Docker-behållare på Azure. Den här självstudiekursen lär du dig. Den använder Windows CICS (kontroll Kundinformationssystem) acctdemo demonstration för Enterprise Server.

Docker lägger till portabilitet och isolering program. Exempelvis kan du exportera en Docker-avbildning från en virtuell Windows-dator ska köras på en annan, eller från en databas till en Windows-server med Docker. Docker-avbildningen som körs på den nya platsen med samma konfiguration – utan att behöva installera Enterprise Server. Det är en del av avbildningen. Licensiering fortfarande gäller.

Den här självstudien installerar den **Windows 2016 Datacenter med behållare** virtuell dator (VM) från Azure Marketplace. Den här virtuella datorn innehåller **Docker 18.09.0**. Stegen nedan visar hur du distribuerar du behållaren, köra den och sedan ansluta till den med en 3270 emulator.

## <a name="prerequisites"></a>Förutsättningar

Kolla in dessa krav innan du börjar:

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Programvaran Micro fokus och en giltig licens (eller utvärderingslicens). Om du är en befintlig Micro fokus-kund kan du kontakta din Micro fokus-representant. I annat fall [begär en utvärderingsversion](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Demonstration dockerfiler ingår i Enterprise Server 4.0. Den här självstudien används ent\_server\_dockerfiles\_4.0\_windows.zip. Komma åt den från samma plats som du öppnade Enterprise Server-installationsfilen, eller gå till *Micro fokus* att komma igång.

- I dokumentationen för [Enterprise Server och företagsutvecklare](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Skapa en virtuell dator

1. Skydda mediet från ent\_server\_dockerfiles\_4.0\_windows.zip fil. Skydda ES-Docker-Prod-XXXXXXXX.mflic licensiering filen (krävs för att skapa Docker-avbildningar).

2. Skapa den virtuella datorn. Gör detta genom att öppna Azure portal, Välj **skapa en resurs** från överst till vänster och filtrera efter *windows server*. I resultatet väljer **Windows Server 2016 Datacenter – med behållare**.

     ![Sökresultat för Azure](media/01-portal.png)

3. Välj instansinformation för att konfigurera egenskaperna för den virtuella datorn:

    1. Välj storlek på den virtuella datorn. Överväg att använda den här självstudien en **Standard DS2\_v2** virtuell dator med 2 virtuella processorer och 7 GB minne.

    2. Välj den **Region** och **resursgrupp** du vill distribuera till.

    3. För **tillgänglighetsalternativ**, Använd standardinställningen.

    4. För **användarnamn**, skriver du vill använda administratörskontot och lösenordet.

    5. Se till att **port 3389 RDP** är öppen. Endast den här porten måste exponeras offentligt, så du kan logga in till den virtuella datorn. Sedan acceptera alla standardvärden och klicka på **granska + skapa**.

     ![Skapa en virtuell dator-fönstret](media/container-02.png)

4. Vänta tills distributionen är klar (ett par minuter). Ett meddelande om att den virtuella datorn har skapats.

5. Klicka på **gå till resurs** att gå till den **översikt** bladet för den virtuella datorn.

6. Till höger klickar du på den **Connect** knappen. Den **Anslut till den virtuella datorn** alternativ visas till höger.

7. Klicka på den **ladda ned RDP-filen** för att ladda ned RDP-filen som gör det möjligt att ansluta till den virtuella datorn.

8. När filen har hämtats, öppnar du den och anger användarnamn och lösenord som du skapade för den virtuella datorn.

     > [!NOTE]
     > Använd inte företagets autentiseringsuppgifter för inloggning. (RDP-klient förutsätter att du kanske vill använda dessa. Du inte.)

9.  Välj **fler alternativ**, välj sedan dina autentiseringsuppgifter för virtuell dator.

Nu är den virtuella datorn körs och anslutna via RDP. Du är inloggad i och är redo för nästa steg.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Skapa en sandbox-katalog och ladda upp zip-filen

1.  Skapa en katalog på den virtuella datorn där du kan ladda upp filer Demonstrations- och licens. Till exempel **C:\\Sandbox**.

2.  Ladda upp **entinstallationer\_server\_dockerfiles\_4.0\_windows.zip** och **ES-Docker-Prod-XXXXXXXX.mflic** filen till den katalog som du skapade.

3.  Extrahera innehållet i zip-filen till den **entinstallationer\_server\_dockerfiles\_4.0\_windows** directory som skapats av extraheringsprocessen. Den här katalogen innehåller en Viktigt-fil (som .html och .txt-fil) och två underkataloger **EnterpriseServer** och **exempel**.

4.  Kopiera **ES-Docker-Prod-XXXXXXXX.mflic** till enhet C:\\Sandbox\\entinstallationer\_server\_dockerfiles\_4.0\_windows\\ EnterpriseServer och C:\\Sandbox\\entinstallationer\_server\_dockerfiles\_4.0\_windows\\exempel\\CICS kataloger.

> [!NOTE]
> Kontrollera att du kopiera filen licensiering till båda katalogerna. De är obligatoriska för ett byggsteg för Docker att kontrollera att bilderna är korrekt licensierade.

## <a name="check-docker-version-and-create-base-image"></a>Kontrollera Docker-version och skapa basavbildningen

> [!IMPORTANT]
> Skapa en lämplig avbildning med Docker är en tvåstegsprocess. Skapa först basavbildningen Enterprise Server 4.0. Skapa sedan en annan avbildning för x64 plattform. Men du kan skapa en x86 (32-bitars) bild ska du använda 64-bitarsbild.

1. Öppna en kommandotolk.

2. Kontrollera att Docker är installerat. Skriv följande i kommandotolken:

    ```
        docker version
    ```

     Till exempel var versionen 18.09.0 när detta skrevs.

3. Om du vill ändra katalog, Skriv **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Typ **bld.bat IacceptEULA** att påbörja skapandeprocessen för inledande basavbildning. Vänta några minuter för den här processen att köra. I resultatet, Lägg märke till de två bilder som har skapats – en för x64 och en för x86:

     ![Kommandofönstret visar avbildningar](media/container-04.png)

5. Om du vill skapa den slutliga avbildningen för CICS demonstration, växla till katalogen CICS genom att skriva **cd\\Sandbox\\entinstallationer\_server\_dockerfiles\_4.0\_windows\\ Exempel\\CICS**.

6. För att skapa avbildningen skriver **bld.bat x64**. Vänta några minuter innan processen för att köra och meddelande som säger att avbildningen har skapats.

7. Typ **docker-avbildningar** att visa en lista med alla Docker-avbildningar som är installerad på den virtuella datorn. Se till att **microfocus/es-acctdemo** är en av dem.

     ![Kommandofönstret visar es acctdemo bild](media/container-05.png)

## <a name="run-the-image"></a>Kör avbildningen 

1.  Att starta Enterprise Server 4.0 och programmet acctdemo kommandotolk skriver:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installera en terminalemulator 3270 t.ex [x3270](http://x3270.bgp.nu/) och använda den för att ansluta via port 9040, att den avbildning som körs.

3.  Hämta IP-adressen för behållaren acctdemo så Docker kan fungera som en DHCP-server för de behållare som den hanterar:

    1.  Hämta ID för behållaren som körs. Typ **Docker ps** vid kommandotolken och anteckna ID: T (**22a0fe3159d0** i det här exemplet). Spara den till nästa steg.

    2.  Hämta IP-adressen för acctdemo behållaren med behållar-ID från föregående steg på följande sätt:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Exempel:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Notera IP-adressen för acctdemo avbildningen. Adressen i följande utdata är till exempel 172.19.202.52.

     ![Kommandofönstret visar IP-adress](media/container-06.png)

5. Montera avbildningen med hjälp av emulatorn. Konfigurera emulatorn att använda adressen av acctdemo avbildningen och port 9040. Här är den har **172.19.202.52:9040**. Din kommer att bli liknande. Den **inloggning till CICS** skärmen öppnas.

    ![Inloggning till CICS skärm](media/container-07.png)

6. Logga in på regionen CICS genom att ange **SYSAD** för den **USERID** och **SYSAD** för den **lösenord**.

7. Rensa skärmen, med hjälp av den emulatorn keymap. X3270, Välj den **Keymap** menyalternativ.

8. Om du vill starta programmet acctdemo skriver **ACCT**. Den första skärmen för programmet visas.

     ![Konto Demo-skärmen](media/container-08.png)

9. Experimentera med Visa kontotyper. Skriv exempelvis **D** för begäran och **11111** för den **konto**. Andra kontonummer att försöka är 22222, 33333 och så vidare.

     ![Konto Demo-skärmen](media/container-09.png)

10. Om du vill visa Enterprise Server-Administrationskonsolen, gå till Kommandotolken och skriv **starta http:172.19.202.52:86**

     ![Enterprise Server-administrationskonsolen](media/container-010.png)

Klart! Nu du kör och hantera ett CICS-program i en Docker-behållare.

## <a name="next-steps"></a>Nästa steg

- [Installera Micro fokus Enterprise Server 4.0 och företagsutvecklare 4.0 på Azure](./set-up-micro-focus-azure.md)
- [Stordatormigrering för program](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
