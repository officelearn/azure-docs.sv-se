---
title: Beräkningsintensiva Java-program på en virtuell dator
description: Lär dig hur du skapar en Azure-dator som kör ett beräkningsintensiva Java-program som kan övervakas av ett annat Java-program.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management,azure-resource-manager
ms.assetid: ae6f2737-94c7-4569-9913-d871450c2827
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e8da296c30f1d2be3c637e456e90d2f93da67548
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528468"
---
# <a name="how-to-run-a-compute-intensive-task-in-java-on-a-virtual-machine"></a>Så kör du en beräkningsintensiv uppgift i Java på en virtuell dator
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Med Azure, kan du använda en virtuell dator för att hantera beräkningsintensiva aktiviteter. En virtuell dator kan hantera aktiviteter och leverera resultat till klientdatorer och mobila program. När du har läst den här artikeln kommer du ha en förståelse för hur du skapar en virtuell dator som kör ett beräkningsintensiva Java-program som kan övervakas av ett annat Java-program.

Den här kursen förutsätter att du vet hur du skapar konsolen Java-program kan importera bibliotek för Java-programmet och kan generera ett Java-Arkiv (JAR). Ingen kunskap om Microsoft Azure antas.

Du kommer att lära dig:

* Så här skapar du en virtuell dator med en Java Development Kit (JDK) redan har installerats.
* Hur du loggar in via en fjärranslutning till den virtuella datorn.
* Så här skapar du en service bus-namnrymd.
* Hur du skapar en Java-program som utför en krävande uppgift.
* Hur du skapar en Java-program som övervakar statusen för beräkningsintensiva-aktivitet.
* Så här kör du Java-program.
* Hur du stoppar Java-program.

Den här kursen använder Traveling säljare problemet för aktiviteten beräkningsintensiva. Följande är ett exempel på Java-program krävande uppgift körs.

![Traveling säljare problemet solver][solver_output]

Följande är ett exempel på Java-program krävande aktiviteten för övervakning.

![Klienten Traveling säljare Problem][client_output]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Skapa en virtuell dator
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapar du en resurs**, klickar du på **Compute**, klickar du på **virtuella**, och klicka sedan på **från galleriet**.
3. I den **virtuella avbildning väljer** dialogrutan **JDK 7 Windows Server 2012**.
   Observera att **JDK 6 Windows Server 2012** är tillgänglig om du har äldre program som inte ännu är redo att köras i JDK 7.
4. Klicka på **Nästa**.
5. I den **konfiguration av virtuell dator** dialogrutan:
   1. Ange ett namn för den virtuella datorn.
   2. Ange storleken som ska användas för den virtuella datorn.
   3. Ange ett namn för administratören i den **användarnamn** fältet. Kom ihåg detta namn och lösenord anger du sedan kan du använda dem när du logga på den virtuella datorn.
   4. Ange ett lösenord i den **nytt lösenord** och ange den på nytt i **Bekräfta** fältet. Det här är lösenordet för administratörskontot.
   5. Klicka på **Nästa**.
6. I nästa **konfiguration av virtuell dator** dialogrutan:
   1. För **Molntjänsten**, använder du standard **skapa en ny molntjänst**.
   2. Värdet för **DNS molntjänstnamnet** måste vara unikt inom cloudapp.net. Om det behövs, ändra värdet så att Azure anger att det är unikt.
   3. Ange en region, en tillhörighetsgrupp eller ett virtuellt nätverk. För den här kursen är att ange en region som **västra USA**.
   4. För **Lagringskonto**väljer **använda ett automatiskt genererat lagringskonto**.
   5. För **Tillgänglighetsuppsättning**väljer **(ingen)**.
   6. Klicka på **Nästa**.
7. I slutliga **konfiguration av virtuell dator** dialogrutan:
   1. Acceptera standardvärden för slutpunkten.
   2. Klicka på **Complete** (Slutför).

## <a name="to-remotely-log-in-to-your-virtual-machine"></a>Logga in via fjärranslutning på den virtuella datorn
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **virtuella datorer**.
3. Klicka på namnet på den virtuella dator som du vill logga in på.
4. Klicka på **Anslut**.
5. Svara på frågorna som behövs för att ansluta till den virtuella datorn. Använd värdena som du angav när du skapade den virtuella datorn när du uppmanas administratörens namn och lösenord.

Observera att Azure Service Bus-funktioner kräver Baltimore CyberTrust-rotcertifikatet installeras som en del av din JRE **cacerts** lagras. Det här certifikatet ingår automatiskt i den miljö JRE (Java Runtime) används av den här kursen. Om du inte har det här certifikatet i dina JRE **cacerts** finns i [att lägga till ett certifikat i certifikatarkivet för Certifikatutfärdaren i Java] [ add_ca_cert] information om att lägga till den (samt information om hur du visar certifikat i din cacerts store).

## <a name="how-to-create-a-service-bus-namespace"></a>Så här skapar du en service bus-namnrymd
För att komma igång med Service Bus-köer i Azure, måste du först skapa ett namnområde för tjänsten. Ett namnområde för tjänsten innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Gör så här för att skapa ett namnområde för tjänsten:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I nedre vänstra navigeringsfönstret i Azure-portalen klickar du på **Service Bus, Access Control och cachelagring**.
3. I rutan längst upp till vänster i Azure-portalen klickar du på den **Service Bus** noden och klicka sedan på den **ny** knappen.  
   ![Skärmbild för Service Bus-nod][svc_bus_node]
4. I den **skapa en ny Service Namespace** dialogrutan Ange en **Namespace**, och klicka sedan på om du vill kontrollera att det är unikt i **Kontrollera tillgänglighet** knappen.  
   ![Skapa en ny Namespace skärmbild][create_namespace]
5. Efter att kontrollera att namnet på namnområdet är tillgängligt, väljer du land eller region där namnområdet ska finnas och klicka sedan på den **skapa Namespace** knappen.  
   
   Det namnområde som du skapat visas sedan i Azure-portalen och tar en stund innan det aktiveras. Vänta tills statusen blir **Active** innan du fortsätter med nästa steg.

## <a name="obtain-the-default-management-credentials-for-the-namespace"></a>Hämta standard autentiseringsuppgifter för hantering av namnområdet
För att kunna utföra hanteringsåtgärder, till exempel skapa en kö på det nya namnområdet som du behöver skaffa autentiseringsuppgifter för hantering för namnområdet.

1. I det vänstra navigeringsfönstret klickar du på den **Service Bus** noder för att visa listan över tillgängliga namnområden.
   ![Tillgängliga namnområden skärmbild][avail_namespaces]
2. Markera det namnområde som du nyss skapat från listan som visas.
   ![Namespace-listan skärmbild][namespace_list]
3. Den högra **egenskaper** fönstret visas egenskaperna för det nya namnområdet.
   ![Egenskaper för fönstret skärmbild][properties_pane]
4. Den **standard nyckeln** är dolt. Klicka på den **visa** för att visa säkerhetsreferenser.
   ![Skärmbild som standard nyckel][default_key]
5. Anteckna den **standard utfärdaren** och **standard nyckeln** eftersom du ska använda den här informationen nedan för att utföra åtgärder med namnområdet.

## <a name="how-to-create-a-java-application-that-performs-a-compute-intensive-task"></a>Så här skapar du en Java-program som utför en krävande uppgift
1. På utvecklingsdatorn (som inte behöver vara den virtuella datorn som du skapade), ladda ned den [Azure SDK för Java](https://azure.microsoft.com/develop/java/).
2. Skapa ett Java-konsolprogram med hjälp av koden i slutet av det här avsnittet. I den här kursen använder vi **TSPSolver.java** som Java-filnamn. Ändra den **din\_service\_bus\_namnområde**, **din\_service\_bus\_ägare**, och **din\_service\_bus\_nyckeln** platshållare för att använda service bus **namnområde**, **standard utfärdaren** och **standard nyckeln** värdena respektive.
3. Exportera det till ett körbara Java-Arkiv (JAR) efter kodning, och paketera bibliotek som krävs till den genererade JAR. I den här kursen använder vi **TSPSolver.jar** som genererade JAR-namn.

<p/>

    // TSPSolver.java

    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import java.io.*;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import java.util.ArrayList;
    import java.util.Date;
    import java.util.List;

    public class TSPSolver {

        //  Value specifying how often to provide an update to the console.
        private static long loopCheck = 100000000;  

        private static long nTimes = 0, nLoops=0;

        private static double[][] distances;
        private static String[] cityNames;
        private static int[] bestOrder;
        private static double minDistance;
        private static ServiceBusContract service;

        private static void buildDistances(String fileLocation, int numCities) throws Exception{
            try{
                BufferedReader file = new BufferedReader(new InputStreamReader(new DataInputStream(new FileInputStream(new File(fileLocation)))));
                double[][] cityLocs = new double[numCities][2];
                for (int i = 0; i<numCities; i++){
                    String[] line = file.readLine().split(", ");
                    cityNames[i] = line[0];
                    cityLocs[i][0] = Double.parseDouble(line[1]);
                    cityLocs[i][1] = Double.parseDouble(line[2]);
                }
                for (int i = 0; i<numCities; i++){
                    for (int j = i; j<numCities; j++){
                        distances[i][j] = Math.hypot(Math.abs(cityLocs[i][0] - cityLocs[j][0]), Math.abs(cityLocs[i][1] - cityLocs[j][1]));
                        distances[j][i] = distances[i][j];
                    }
                }
            } catch (Exception e){
                throw e;
            }
        }

        private static void permutation(List<Integer> startCities, double distSoFar, List<Integer> restCities) throws Exception {

            try
            {
                nTimes++;
                if (nTimes == loopCheck)
                {
                    nLoops++;
                    nTimes = 0;
                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.print("Current time is " + dateFormat.format(date) + ". ");
                    System.out.println(  "Completed " + nLoops + " iterations of size of " + loopCheck + ".");
                }

                if ((restCities.size() == 1) && ((minDistance == -1) || (distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-1)] < minDistance))){
                    startCities.add(restCities.get(0));
                    newBestDistance(startCities, distSoFar + distances[restCities.get(0)][startCities.get(0)] + distances[restCities.get(0)][startCities.get(startCities.size()-2)]);
                    startCities.remove(startCities.size()-1);
                }
                else{
                    for (int i=0; i<restCities.size(); i++){
                        startCities.add(restCities.get(0));
                        restCities.remove(0);
                        permutation(startCities, distSoFar + distances[startCities.get(startCities.size()-1)][startCities.get(startCities.size()-2)],restCities);
                        restCities.add(startCities.get(startCities.size()-1));
                        startCities.remove(startCities.size()-1);
                    }
                }
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        private static void newBestDistance(List<Integer> cities, double distance) throws ServiceException, Exception {
            try
            {
                minDistance = distance;
                String cityList = "Shortest distance is "+minDistance+", with route: ";
                for (int i = 0; i<bestOrder.length; i++){
                    bestOrder[i] = cities.get(i);
                    cityList += cityNames[bestOrder[i]];
                    if (i != bestOrder.length -1)
                        cityList += ", ";
                }
                System.out.println(cityList);
                service.sendQueueMessage("TSPQueue", new BrokeredMessage(cityList));
            }
            catch (ServiceException se)
            {
                throw se;
            }
            catch (Exception e)
            {
                throw e;
            }
        }

        public static void main(String args[]){

            try {

                Configuration config = ServiceBusConfiguration.configureWithWrapAuthentication(
                        "your_service_bus_namespace", "your_service_bus_owner",
                        "your_service_bus_key",
                        ".servicebus.windows.net",
                        "-sb.accesscontrol.windows.net/WRAPv0.9");

                service = ServiceBusService.create(config);

                int numCities = 10;  // Use as the default, if no value is specified at command line.
                if (args.length != 0)
                {
                    if (args[0].toLowerCase().compareTo("createqueue")==0)
                    {
                        // No processing to occur other than creating the queue.
                        QueueInfo queueInfo = new QueueInfo("TSPQueue");

                        service.createQueue(queueInfo);

                        System.out.println("Queue named TSPQueue was created.");

                        System.exit(0);
                    }

                    if (args[0].toLowerCase().compareTo("deletequeue")==0)
                    {
                        // No processing to occur other than deleting the queue.
                        service.deleteQueue("TSPQueue");

                        System.out.println("Queue named TSPQueue was deleted.");

                        System.exit(0);
                    }

                    // Neither creating or deleting a queue.
                    // Assume the value passed in is the number of cities to solve.
                    numCities = Integer.valueOf(args[0]);  
                }

                System.out.println("Running for " + numCities + " cities.");

                List<Integer> startCities = new ArrayList<Integer>();
                List<Integer> restCities = new ArrayList<Integer>();
                startCities.add(0);
                for(int i = 1; i<numCities; i++)
                    restCities.add(i);
                distances = new double[numCities][numCities];
                cityNames = new String[numCities];
                buildDistances("c:\\TSP\\cities.txt", numCities);
                minDistance = -1;
                bestOrder = new int[numCities];
                permutation(startCities, 0, restCities);
                System.out.println("Final solution found!");
                service.sendQueueMessage("TSPQueue", new BrokeredMessage("Complete"));
            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }
        }

    }



## <a name="how-to-create-a-java-application-that-monitors-the-progress-of-the-compute-intensive-task"></a>Så här skapar du ett Java-program som övervakar statusen för beräkningsintensiva-aktivitet
1. Skapa ett konsolprogram i Java med hjälp av koden i slutet av det här avsnittet på utvecklingsdatorn. I den här kursen använder vi **TSPClient.java** som Java-filnamn. Enligt tidigare, kan du ändra den **din\_service\_bus\_namnområde**, **din\_service\_bus\_ägare**, och **din\_service\_bus\_nyckeln** platshållare för att använda service bus **namnområde**, **standard utfärdaren** och **standard nyckeln** värdena respektive.
2. Exportera det till en körbara JAR och paketera bibliotek som krävs till den genererade JAR. I den här kursen använder vi **TSPClient.jar** som genererade JAR-namn.

<p/>

    // TSPClient.java

    import java.util.Date;
    import java.text.DateFormat;
    import java.text.SimpleDateFormat;
    import com.microsoft.windowsazure.services.serviceBus.*;
    import com.microsoft.windowsazure.services.serviceBus.models.*;
    import com.microsoft.windowsazure.services.core.*;

    public class TSPClient
    {

        public static void main(String[] args)
        {
                try
                {

                    DateFormat dateFormat = new SimpleDateFormat("MM/dd/yyyy HH:mm:ss");
                    Date date = new Date();
                    System.out.println("Starting at " + dateFormat.format(date) + ".");

                    String namespace = "your_service_bus_namespace";
                    String issuer = "your_service_bus_owner";
                    String key = "your_service_bus_key";

                    Configuration config;
                    config = ServiceBusConfiguration.configureWithWrapAuthentication(
                            namespace, issuer, key,
                            ".servicebus.windows.net",
                            "-sb.accesscontrol.windows.net/WRAPv0.9");

                    ServiceBusContract service = ServiceBusService.create(config);

                    BrokeredMessage message;

                    int waitMinutes = 3;  // Use as the default, if no value is specified at command line.
                    if (args.length != 0)
                    {
                        waitMinutes = Integer.valueOf(args[0]);  
                    }

                    String waitString;

                    waitString = (waitMinutes == 1) ? "minute." : waitMinutes + " minutes.";

                    // This queue must have previously been created.
                    service.getQueue("TSPQueue");

                    int numRead;

                    String s = null;

                    while (true)
                    {

                        ReceiveQueueMessageResult resultQM = service.receiveQueueMessage("TSPQueue");
                        message = resultQM.getValue();

                        if (null != message && null != message.getMessageId())
                        {

                            // Display the queue message.
                            byte[] b = new byte[200];

                            System.out.print("From queue: ");

                            s = null;
                            numRead = message.getBody().read(b);
                            while (-1 != numRead)
                            {
                                s = new String(b);
                                s = s.trim();
                                System.out.print(s);
                                numRead = message.getBody().read(b);
                            }
                            System.out.println();
                            if (s.compareTo("Complete") == 0)
                            {
                                // No more processing to occur.
                                date = new Date();
                                System.out.println("Finished at " + dateFormat.format(date) + ".");
                                break;
                            }
                        }
                        else
                        {
                            // The queue is empty.
                            System.out.println("Queue is empty. Sleeping for another " + waitString);
                            Thread.sleep(60000 * waitMinutes);
                        }
                    }

            }
            catch (ServiceException se)
            {
                System.out.println(se.getMessage());
                se.printStackTrace();
                System.exit(-1);
            }
            catch (Exception e)
            {
                System.out.println(e.getMessage());
                e.printStackTrace();
                System.exit(-1);
            }

        }

    }

## <a name="how-to-run-the-java-applications"></a>Så här kör du Java-program
Kör programmet beräkningsintensiva först för att skapa kön, sedan för att lösa reser Saleseman Problem, som lägger till den aktuella bästa vägen service bus-kö. Medan programmet beräkningsintensiva körs (eller senare), kör klienten för att visa resultat från service bus-kö.

### <a name="to-run-the-compute-intensive-application"></a>Att köra programmet beräkningsintensiva
1. Logga in på den virtuella datorn.
2. Skapa en mapp där du kör programmet. Till exempel **c:\TSP**.
3. Kopiera **TSPSolver.jar** till **c:\TSP**,
4. Skapa en fil med namnet **c:\TSP\cities.txt** med följande innehåll.
   
        City_1, 1002.81, -1841.35
        City_2, -953.55, -229.6
        City_3, -1363.11, -1027.72
        City_4, -1884.47, -1616.16
        City_5, 1603.08, -1030.03
        City_6, -1555.58, 218.58
        City_7, 578.8, -12.87
        City_8, 1350.76, 77.79
        City_9, 293.36, -1820.01
        City_10, 1883.14, 1637.28
        City_11, -1271.41, -1670.5
        City_12, 1475.99, 225.35
        City_13, 1250.78, 379.98
        City_14, 1305.77, 569.75
        City_15, 230.77, 231.58
        City_16, -822.63, -544.68
        City_17, -817.54, -81.92
        City_18, 303.99, -1823.43
        City_19, 239.95, 1007.91
        City_20, -1302.92, 150.39
        City_21, -116.11, 1933.01
        City_22, 382.64, 835.09
        City_23, -580.28, 1040.04
        City_24, 205.55, -264.23
        City_25, -238.81, -576.48
        City_26, -1722.9, -909.65
        City_27, 445.22, 1427.28
        City_28, 513.17, 1828.72
        City_29, 1750.68, -1668.1
        City_30, 1705.09, -309.35
        City_31, -167.34, 1003.76
        City_32, -1162.85, -1674.33
        City_33, 1490.32, 821.04
        City_34, 1208.32, 1523.3
        City_35, 18.04, 1857.11
        City_36, 1852.46, 1647.75
        City_37, -167.44, -336.39
        City_38, 115.4, 0.2
        City_39, -66.96, 917.73
        City_40, 915.96, 474.1
        City_41, 140.03, 725.22
        City_42, -1582.68, 1608.88
        City_43, -567.51, 1253.83
        City_44, 1956.36, 830.92
        City_45, -233.38, 909.93
        City_46, -1750.45, 1940.76
        City_47, 405.81, 421.84
        City_48, 363.68, 768.21
        City_49, -120.3, -463.13
        City_50, 588.51, 679.33
5. I Kommandotolken, ändra kataloger till c:\TSP.
6. Kontrollera den JRE bin-mappen är i PATH-miljövariabeln.
7. Du behöver skapa service bus-kö innan du kör Telefontjänstprovider solver permutationer. Kör följande kommando för att skapa service bus-kö.
   
        java -jar TSPSolver.jar createqueue
8. Nu när kön har skapats kan köra du Telefontjänstprovider solver permutationer. Till exempel köra följande kommando för att köra solver för 8 städer.
   
        java -jar TSPSolver.jar 8
   
   Om du inte anger ett tal, körs efter 10 städer. Eftersom solver hittar aktuella kortaste vägar, läggs de till kön.

> [!NOTE]
> Ju fler som du anger, desto längre tid solver körs. Till exempel köra för 14 orter kan ta flera minuter och köras för 15 orter kan ta flera timmar. Öka till 16 eller flera orter resultera i dagar för körning (slutligen veckor, månader och år). Detta beror på den snabba ökningen av antalet permutationer som utvärderas i skiljde som antalet orter ökar.
> 
> 

### <a name="how-to-run-the-monitoring-client-application"></a>Hur du kör klientprogrammet övervakning
1. Logga in på datorn där du ska köra klientprogrammet. Det behöver inte vara samma dator som kör den **TSPSolver** program, även om det kan vara.
2. Skapa en mapp där du kör programmet. Till exempel **c:\TSP**.
3. Kopiera **TSPClient.jar** till **c:\TSP**,
4. Kontrollera den JRE bin-mappen är i PATH-miljövariabeln.
5. I Kommandotolken, ändra kataloger till c:\TSP.
6. Kör följande kommando.
   
        java -jar TSPClient.jar
   
    Du kan också ange antalet minuter för strömsparläge between kontrollerar kön genom att passera i ett kommandoradsargument. Standardvärdet för strömsparläge för att kontrollera kön är 3 minuter, som används om inget kommandoradsargument som skickas till **TSPClient**. Om du vill använda ett annat värde för strömsparläge intervallet exempelvis en minut att köra följande kommando.
   
        java -jar TSPClient.jar 1
   
    Klienten körs förrän den ser ett kömeddelande på ”Slutför”. Observera att om du kör flera förekomster av solver utan att köra klienten, du kan behöva köra klienten flera gånger för att helt tömma kön. Alternativt kan du ta bort kön och sedan skapa den igen. Om du vill ta bort kön, kör du följande **TSPSolver** (inte **TSPClient**) kommando.
   
        java -jar TSPSolver.jar deletequeue
   
    Solver kommer att köras tills det är färdigt undersöka alla vägar.

## <a name="how-to-stop-the-java-applications"></a>Hur du stoppar Java-program
För både solver och klientprogram kan du trycka på **Ctrl + C** att avsluta om du vill avsluta innan normal slutförande.

[solver_output]:media/java-run-compute-intensive-task/WA_JavaTSPSolver.png
[client_output]:media/java-run-compute-intensive-task/WA_JavaTSPClient.png
[svc_bus_node]:media/java-run-compute-intensive-task/SvcBusQueues_02_SvcBusNode.jpg
[create_namespace]:media/java-run-compute-intensive-task/SvcBusQueues_03_CreateNewSvcNamespace.jpg
[avail_namespaces]:media/java-run-compute-intensive-task/SvcBusQueues_04_SvcBusNode_AvailNamespaces.jpg
[namespace_list]:media/java-run-compute-intensive-task/SvcBusQueues_05_NamespaceList.jpg
[properties_pane]:media/java-run-compute-intensive-task/SvcBusQueues_06_PropertiesPane.jpg
[default_key]:media/java-run-compute-intensive-task/SvcBusQueues_07_DefaultKey.jpg
[add_ca_cert]: ../../../java-add-certificate-ca-store.md
