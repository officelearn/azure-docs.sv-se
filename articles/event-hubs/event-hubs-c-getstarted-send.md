---
title: 'Snabbstart: Skicka händelser med C - Azure Event Hubs'
description: 'Snabbstart: Den här artikeln innehåller en genomgång för att skapa ett C-program som skickar händelser till Azure Event Hubs.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720677"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Snabbstart: Skicka händelser till Azure Event Hubs med C

## <a name="introduction"></a>Introduktion
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skickar händelser till en händelsehubb med hjälp av ett konsolprogram i C. 

## <a name="prerequisites"></a>Krav
För att kunna genomföra den här kursen behöver du följande:

* En C-utvecklingsmiljö. Den här självstudien förutsätter gcc-stacken på en Virtuell Azure Linux-dator med Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Skapa ett namnområde för händelsehubbar och en händelsehubb**. Använd [Azure-portalen](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de hanteringsautentiseringsuppgifter som ditt program behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta värdet för åtkomstnyckeln för händelsehubben genom att följa instruktionerna i artikeln: [Hämta anslutningssträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomstnyckeln i koden du skriver senare i den här självstudien. Standardnyckelnamnet är: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Skriv kod för att skicka meddelanden till eventhubbar
I det här avsnittet visas hur du skriver en C-app för att skicka händelser till din händelsehubb. Koden använder Proton AMQP-biblioteket från [Apache Qpid-projektet](https://qpid.apache.org/). Detta är analogt med att använda Service Bus-köer och ämnen med AMQP från C som visas [i det här exemplet](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Mer information finns i [Qpid Proton-dokumentationen](https://qpid.apache.org/proton/index.html).

1. På [Qpid AMQP Messenger-sidan](https://qpid.apache.org/proton/messenger.html)följer du instruktionerna för att installera Qpid Proton, beroende på din miljö.
2. Så här kompilerar du Proton-biblioteket:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Ladda ner [Qpid Proton-biblioteket](https://qpid.apache.org/proton/index.html)och extrahera det, t.ex.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Skapa en byggkatalog, kompilera och installera:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Skapa en ny fil som heter **sender.c** i arbetskatalogen med följande kod. Kom ihåg att ersätta värdena för DIN SAS-nyckel/namn, händelsenavnamn och namnområde. Du måste också ersätta en URL-kodad version av nyckeln för **SendRule** som skapats tidigare. Du kan URL-koda det [här](https://www.w3schools.com/tags/ref_urlencode.asp).
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Kompilera filen, förutsatt **gcc:**
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Den här koden använder ett utgående fönster på 1 för att tvinga ut meddelandena så snart som möjligt. Vi rekommenderar att programmet försöker batchmeddelanden för att öka dataflödet. Se [Qpid AMQP Messenger-sidan](https://qpid.apache.org/proton/messenger.html) för information om hur du använder Qpid Proton-biblioteket i den här och andra miljöer och från plattformar för vilka bindningar tillhandahålls (för närvarande Perl, PHP, Python och Ruby).

Kör programmet för att skicka meddelanden till händelsehubben. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
