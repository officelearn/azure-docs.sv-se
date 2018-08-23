---
title: Skicka händelser till Azure Event Hubs med C | Microsoft Docs
description: Skicka händelser till Azure Event Hubs med hjälp av C
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
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 262f274541f486f5457ef8eae6fd4f60fb34824e
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "42055216"
---
# <a name="send-events-to-azure-event-hubs-using-c"></a>Skicka händelser till Azure Event Hubs med hjälp av C

## <a name="introduction"></a>Introduktion
Händelsehubbar är en mycket skalbar inmatning system som kan mata in miljontals händelser per sekund, aktivera ett program för att bearbeta och analysera de enorma mängder data som produceras av dina anslutna enheter och program. När samlats in i en händelsehubb, kan du omvandla och lagra dem med valfri leverantör av realtidsanalys eller lagringskluster.

Mer information finns i den [översikt av Händelsehubbar](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview).

Den här självstudien beskrivs hur du skickar händelser till en händelsehubb med ett konsolprogram i C. Mer information om att ta emot händelser, klickar du på ditt mottagarspråk i den vänstra innehållsförteckningen.

För att kunna genomföra den här kursen behöver du följande:

* En C-utvecklingsmiljö. Den här självstudiekursen förutsätter gcc-stacken på en virtuell Linux-dator med Ubuntu 14.04.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* Ett aktivt Azure-konto. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="send-messages-to-event-hubs"></a>Skicka meddelanden till Event Hubs
I det här avsnittet visar hur du skriver en app för C för att skicka händelser till din event hub. Koden använder Proton AMQP-biblioteket från den [Apache Qpid projekt](http://qpid.apache.org/). Detta motsvarar att använda Service Bus-köer och ämnen med AMQP från C enligt [i det här exemplet](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Mer information finns i den [Qpid Proton dokumentation](http://qpid.apache.org/proton/index.html).

1. Från den [Qpid AMQP Messenger sidan](https://qpid.apache.org/proton/messenger.html), följ instruktionerna för att installera Qpid Proton, beroende på din miljö.
2. Kompilera Proton biblioteket genom att installera följande paket:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Ladda ned den [Qpid Proton biblioteket](http://qpid.apache.org/proton/index.html), och extrahera det, t.ex.:
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Skapa en build-katalog, kompilera och installera:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. Skapa en ny fil med namnet i arbetskatalogen **sender.c** med följande kod. Kom ihåg att ersätta värdena för din SAS-nyckel/name, event hub-namn och namnområde. Du måste också ersätta en URL-kodad version av nyckeln för den **SendRule** skapade tidigare. Du kan URL-koda den [här](http://www.w3schools.com/tags/ref_urlencode.asp).
   
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
6. Kompilera filen, förutsatt att **gcc**:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Den här koden använder ett fönster med en utgående 1 för att tvinga meddelanden ut så snart som möjligt. Du rekommenderas att ditt program försöker att batch-meddelanden för att öka dataflödet. Se den [Qpid AMQP Messenger sidan](https://qpid.apache.org/proton/messenger.html) information om hur du använder Qpid Proton biblioteket på det här och andra miljöer och plattformar som bindningar tillhandahålls (för närvarande Perl, PHP, Python och Ruby).


## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
