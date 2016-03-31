## Senden von Nachrichten an Ereignis-Hubs

In diesem Abschnitt schreiben wir eine C-App, um Ereignisse an den Event Hub zu senden. Wir verwenden die Proton AMQP-Bibliothek die [Apache Qpid-Projekt](http://qpid.apache.org/). Dies ist analog zur Verwendung von Servicebuswarteschlangen und-Themen mit AMQP aus C wie [hier](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Weitere Informationen finden Sie unter [Qpid Proton-Dokumentation](http://qpid.apache.org/proton/index.html).

1. Von der [Seite Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), klicken Sie auf die **Installing Qpid Proton** verknüpfen, und gehen Sie je nach Ihrer Umgebung. Wir setzen eine Linux-Umgebung voraus; Angenommen, ein [virtuellen Linux-Computers](../articles/virtual-machines/virtual-machines-linux-tutorial.md) mit Ubuntu 14.04 ausgegangen.

2. Um die Proton-Bibliothek zu kompilieren, installieren Sie die folgenden Pakete:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Herunterladen der [Qpid Proton-Bibliothek](http://qpid.apache.org/proton/index.html) -Bibliothek, und extrahieren Sie es, z. B.:

    ```
    wget http://apache.fastbull.org/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Erstellen Sie einen Build-Verzeichnis, kompilieren und installieren Sie:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Datei namens **sender.c** mit folgendem Inhalt. Vergessen Sie nicht, den Wert für den Namen des Event Hubs und den Namespacenamen (letzterer lautet i. d. R. `{event hub name}-ns`) zu ersetzen. Ersetzen Sie auch eine URL-codierte Version des Schlüssels für die **SendRule** zuvor erstellt haben. URL-Codierung können sie [hier](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
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
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
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

6. Kompilieren Sie die Datei, sofern **Gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] In diesem Code verwenden wir eine ausgehende Ausgabe 1, um der Meldungen so bald wie möglich zu erzwingen. Im Allgemeinen sollten Anwendungen Nachrichten stapelweise ausgeben, um den Durchsatz zu erhöhen. Finden Sie unter [Seite Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) Weitere Informationen zur Verwendung die Qpid Proton-Bibliothek in dieser und anderen Umgebungen und auf Plattformen, die für die Bindungen bereitgestellt werden (derzeit Perl, PHP, Python und Ruby).


