---
title: Invalidieren zwischengespeicherter Seiten über AEM
description: Erfahren Sie, wie Sie die Interaktion zwischen Dispatcher und AEM konfigurieren, um eine effektive Cache-Verwaltung sicherzustellen.
cmgrlastmodified: 01.11.2007 08 22 29 [aheimoz]
pageversionid: 1193211344162
template: /apps/docs/templates/contentpage
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
exl-id: 90eb6a78-e867-456d-b1cf-f62f49c91851
source-git-commit: 9be9f5935c21ebbf211b5da52280a31772993c2e
workflow-type: tm+mt
source-wordcount: '1407'
ht-degree: 100%

---

# Invalidierung zwischengespeicherter Seiten aus AEM {#invalidating-cached-pages-from-aem}

Bei Verwendung des Dispatchers mit AEM muss die Interaktion konfiguriert werden, um eine effektive Cacheverwaltung zu ermöglichen. Je nach Umgebung kann die Konfiguration auch zu einer Leistungsverbesserung führen.

## Einrichten von AEM-Benutzerkonten  {#setting-up-aem-user-accounts}

Das Standardbenutzerkonto `admin` wird zur Authentifizierung der Replikationsagenten verwendet, die standardmäßig installiert sind. Erstellen Sie ein dediziertes Benutzerkonto zur Verwendung mit Replikationsagenten. 

Weitere Informationen finden Sie im Abschnitt [Konfigurieren von Benutzerreplikation und -transport](https://experienceleague.adobe.com/de/docs/experience-manager-release-information/aem-release-updates/previous-updates/aem-previous-versions#VerificationSteps) der Checkliste für die AEM-Sicherheit.

<!-- OLD URL from above https://helpx.adobe.com/experience-manager/6-3/sites/administering/using/security-checklist.html#VerificationSteps -->

## Invalidierung des Dispatcher-Caches aus der Autorenumgebung {#invalidating-dispatcher-cache-from-the-authoring-environment}

Ein Replikationsagent auf der AEM-Autoreninstanz sendet eine Anforderung zur Invalidierung des Caches an den Dispatcher, wenn eine Seite veröffentlicht wird. Der Dispatcher aktualisiert die Datei schließlich im Cache, wenn neue Inhalte veröffentlicht werden.

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

<!-- 

Comment Type: remark
Last Modified By: unknown unknown (ims-author-0436B4A35714BFF67F000101@AdobeID)
Last Modified Date: 2017-05-25T10:37:23.679-0400

<p>Hiding this information due to <a href="https://jira.corp.adobe.com/browse/CQDOC-5805">CQDOC-5805</a>.</p>

 -->

Gehen Sie wie folgt vor, um einen Replikationsagenten auf der AEM-Autoreninstanz zu konfigurieren. Die Konfiguration macht den Dispatcher-Cache bei Seitenaktivierung ungültig:

1. Öffnen Sie die AEM-Tools-Konsole. (`https://localhost:4502/miscadmin#/etc`)
1. Öffnen Sie den erforderlichen Replikationsagenten unter „Werkzeuge/Replikation/Agenten“ auf der Autoreninstanz. Sie können den Dispatcher Flush-Agenten verwenden, der standardmäßig installiert ist.
1. Klicken Sie auf „Bearbeiten“ und stellen Sie sicher, dass auf der Registerkarte „Einstellungen“ **Aktiviert** ausgewählt ist. 

1. (Optional) Um Alias- oder Vanity Path-Invalidierungsanforderungen zu aktivieren, wählen Sie die Option **Alias-Aktualisierung**.
1. Rufen Sie auf der Registerkarte „Transport“ den Dispatcher auf, indem Sie den URI eingeben.

   Wenn Sie den standardmäßigen Dispatcher-Flush-Agenten verwenden, aktualisieren Sie den Host-Namen und Port, z. B.: https://&lt;*dispatcherHost*>:&lt;*portApache*>/dispatcher/invalidate.cache

   **Hinweis:** Bei Dispatcher-Flush-Agenten wird die URI-Eigenschaft nur verwendet, wenn Sie pfadbasierte Einträge für den virtuellen Host verwenden, um zwischen den Farmen zu unterscheiden. Sie verwenden dieses Feld zum Bestimmen der Farm, die invalidiert werden soll. Beispiel: Farm 1 hat den virtuellen Host `www.mysite.com/path1/*` und Farm 2 den virtuellen Host `www.mysite.com/path2/*`. Mit der URL `/path1/invalidate.cache` können Sie die erste Farm und mit `/path2/invalidate.cache` die zweite Farm bestimmen. Weitere Informationen finden Sie unter [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md).

1. Konfigurieren Sie ggf. weitere Parameter. 
1. Klicken Sie auf „OK“, um den Agenten zu aktivieren.

Sie können den Dispatcher-Flush-Agenten auch über die [Touch-optimierte Benutzeroberfläche von AEM](https://experienceleague.adobe.com/de/docs/experience-manager-65/content/implementing/deploying/configuring/replication#configuring-a-dispatcher-flush-agent) aufrufen und konfigurieren.

Weitere Informationen zum Aktivieren des Zugriffs auf Vanity-URLs finden Sie unter [Aktivieren des Zugriffs auf Vanity-URLs](dispatcher-configuration.md#enabling-access-to-vanity-urls-vanity-urls).

>[!NOTE]
>
>Für den Agenten zum Leeren des Dispatcher-Caches ist kein Benutzername oder Kennwort erforderlich. Diese werden bei entsprechender Konfiguration aber mit Standardauthentifizierung gesendet.

Dieser Ansatz hat zwei mögliche Probleme:

* Der Dispatcher muss über die Autoreninstanz erreichbar sein. Wenn Ihr Netzwerk (z. B. die Firewall) so konfiguriert ist, dass der Zugriff zwischen den beiden eingeschränkt wird, ist dies möglicherweise nicht der Fall.

* Die Veröffentlichung und die Invalidierung des Caches finden gleichzeitig statt. Eine Person kann eine Seite anfordern, nachdem diese gerade aus dem Cache entfernt wurde und kurz bevor die neue Seite veröffentlicht wird. AEM gibt dann die alte Seite zurück und der Dispatcher speichert sie erneut im Cache. Dies stellt vor allem für große Sites ein Problem dar.

## Invalidierung des Dispatcher-Caches von einer Veröffentlichungsinstanz  {#invalidating-dispatcher-cache-from-a-publishing-instance}

Unter bestimmten Umständen lassen sich Leistungsverbesserungen erzielen, indem die Cache-Verwaltung von der Autorenumgebung in eine Veröffentlichungsinstanz verlagert wird. Dann sendet die Veröffentlichungsumgebung (nicht die AEM-Autorenumgebung) eine Anfrage zur Cache-Invalidierung an den Dispatcher, wenn eine veröffentlichte Seite empfangen wird.

Beispiele:

<!-- 

Comment Type: draft

<p>Cache invalidation requests for a page are also generated for any aliases or vanity URLs that are configured in the page properties. </p>

 -->

* Verhindern möglicher Zeitkonflikte zwischen dem Dispatcher und der Veröffentlichungsinstanz (siehe [Invalidierung des Dispatcher-Caches aus der Autorenumgebung](#invalidating-dispatcher-cache-from-the-authoring-environment)).
* Das System umfasst eine Reihe von Veröffentlichungsinstanzen, die sich auf Hochleistungs-Servern befinden, und nur eine Autoreninstanz.

>[!NOTE]
>
>Die Entscheidung, diese Methode zu verwenden, sollte von erfahrenen AEM-Admins getroffen werden.

Ein Replikationsagent, der auf der Veröffentlichungsinstanz ausgeführt wird, steuert die Dispatcher-Leerung. Allerdings erfolgt die Konfiguration in der Autorenumgebung und wird anschließend übertragen, indem der Agent aktiviert wird:

1. Öffnen Sie die AEM-Tools-Konsole.
1. Öffnen Sie den erforderlichen Replikationsagenten unter „Werkzeuge/Replikation/Agenten“ auf der Veröffentlichungsinstanz. Sie können den Dispatcher Flush-Agenten verwenden, der standardmäßig installiert ist.
1. Klicken Sie auf „Bearbeiten“ und stellen Sie sicher, dass auf der Registerkarte „Einstellungen“ **Aktiviert** ausgewählt ist. 
1. (Optional) Um Alias- oder Vanity Path-Invalidierungsanforderungen zu aktivieren, wählen Sie die Option **Alias-Aktualisierung**.
1. Rufen Sie auf der Registerkarte „Transport“ den Dispatcher auf, indem Sie den erforderlichen URI eingeben.\
   Wenn Sie den standardmäßigen Dispatcher-Leerungs-Agenten verwenden, aktualisieren Sie den Host-Namen und den Port; zum Beispiel `http://<dispatcherHost>:<portApache>/dispatcher/invalidate.cache`

   **Hinweis:** Bei Dispatcher-Flush-Agenten wird die URI-Eigenschaft nur verwendet, wenn Sie pfadbasierte Einträge für den virtuellen Host verwenden, um zwischen den Farmen zu unterscheiden. Sie verwenden dieses Feld zum Bestimmen der Farm, die invalidiert werden soll. Beispiel: Farm 1 hat den virtuellen Host `www.mysite.com/path1/*` und Farm 2 den virtuellen Host `www.mysite.com/path2/*`. Mit der URL `/path1/invalidate.cache` können Sie die erste Farm und mit `/path2/invalidate.cache` die zweite Farm bestimmen. Weitere Informationen finden Sie unter [Verwenden des Dispatchers mit mehreren Domänen](dispatcher-domains.md).

1. Konfigurieren Sie ggf. weitere Parameter. 
1. Melden Sie sich bei der Veröffentlichungsinstanz an und validieren Sie die Konfiguration des Flush-Agenten. Stellen Sie außerdem sicher, dass er aktiviert ist.
1. Wiederholen Sie den Vorgang für jede betreffende Veröffentlichungsinstanz.

Wenn Sie nach der Konfiguration eine Seite von Author to Publish aktivieren, initiiert dieser Agent eine Standardreplikation. Das Protokoll enthält Meldungen, die auf Anfragen von Ihrem Publish-Server hinweisen und etwa wie folgt aussehen:

1. `<publishserver> 13:29:47 127.0.0.1 POST /dispatcher/invalidate.cache 200`

## Manuelle Invalidierung des Dispatcher-Caches {#manually-invalidating-the-dispatcher-cache}

Um den Dispatcher-Cache zu invalidieren (oder zu leeren), ohne eine Seite zu aktivieren, können Sie eine HTTP-Anfrage an den Dispatcher ausgeben. Sie können beispielsweise eine AEM-Anwendung erstellen, die es Admins oder anderen Anwendungen ermöglicht, den Cache zu leeren.

Die HTTP-Anfrage führt dazu, dass der AEM-Dispatcher bestimmte Dateien aus dem Cache löscht. Der Dispatcher aktualisiert den Cache dann mit einer neuen Kopie (optional).

### Löschen zwischengespeicherter Dateien  {#delete-cached-files}

Stellen Sie eine HTTP-Anfrage, durch die der Dispatcher Dateien aus dem Cache löscht. Der Dispatcher speichert die Dateien nur dann erneut im Cache, wenn eine Clientanfrage für die Seite eingeht. Auf diese Weise zwischengespeicherte Dateien zu löschen, empfiehlt sich für Websites mit geringer Wahrscheinlichkeit, gleichzeitige Anfragen für ein und dieselbe Seite zu empfangen.

Die HTTP-Anforderung sieht folgendermaßen aus:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
CQ-Handle: path-pattern
Content-Length: 0
```

Der Dispatcher leert (löscht) die zwischengespeicherten Dateien und Ordner mit Namen, die dem Wert des Headers `CQ-Handler` entsprechen. Beispielsweise entspricht der `CQ-Handle` mit dem Wert `/content/geomtrixx-outdoors/en`:

* Allen Dateien (mit beliebiger Dateierweiterung) mit dem Namen `en` im Verzeichnis `geometrixx-outdoors`

* Allen Verzeichnissen mit dem Namen `_jcr_content` unter dem Verzeichnis `en` (das, sofern vorhanden, zwischengespeicherte Renderings von Unterknoten der Seite enthält)

Alle anderen Dateien im Dispatcher-Cache (bzw. bis zu einer bestimmten Ebene je nach `/statfileslevel`-Einstellung) werden durch Änderung der `.stat`-Datei invalidiert. Das Datum der letzten Änderung dieser Datei wird mit dem Datum der letzten Änderung eines zwischengespeicherten Dokuments verglichen. Das Dokument wird erneut abgerufen, wenn die `.stat`-Datei neuer ist. Weitere Informationen finden Sie unter [Invalidierung von Dateien nach Ordnerebene](dispatcher-configuration.md#main-pars_title_26).

Die Invalidierung (d. h. das Ändern von STAT-Dateien) kann durch Senden des zusätzlichen Headers `CQ-Action-Scope: ResourceOnly` verhindert werden. Mit dieser Funktion können bestimmte Ressourcen geleert werden. Alles ohne Invalidierung anderer Teile des Caches, z. B. JSON-Daten. Diese Daten werden dynamisch erstellt und müssen regelmäßig unabhängig vom Cache geleert werden. Zum Beispiel die Darstellung von Daten, die von einem Drittsystem bezogen werden, um Nachrichten, Börsenticker usw. anzuzeigen.

### Löschen und erneutes Zwischenspeichern von Dateien {#delete-and-recache-files}

Geben Sie eine HTTP-Anfrage aus, durch die der Dispatcher zwischengespeicherte Dateien löscht und die Datei unmittelbar abruft und erneut zwischenspeichert. Löschen Sie Dateien und speichern Sie sie sofort erneut im Cache, wenn es wahrscheinlich ist, dass Websites gleichzeitige Anfragen für ein und dieselbe Seite empfangen. Mit dem unmittelbaren erneuten Zwischenspeichern wird sichergestellt, dass der Dispatcher die Seite insgesamt nur einmal abruft und zwischenspeichert, anstatt einmal für jede der gleichzeitigen Client-Anfragen.

**Hinweis:** Das Löschen und erneute Zwischenspeichern von Dateien sollte ausschließlich in der Veröffentlichungsinstanz erfolgen. Wenn dies auf der Autoreninstanz erfolgt, kann es zu Überschneidungen kommen, wenn versucht wird, Ressourcen erneut zwischenzuspeichern, bevor sie veröffentlicht wurden.

Die HTTP-Anforderung sieht folgendermaßen aus:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate   
`Content-Type: text/plain  
CQ-Handle: path-pattern  
Content-Length: numchars in bodypage_path0

page_path1 
...  
page_pathn
```

Die Seitenpfade für das unmittelbare erneute Zwischenspeichern werden im Nachrichtentext in getrennten Zeilen aufgeführt. Der Wert von `CQ-Handle` ist der Pfad einer Seite, die die erneut zwischenzuspeichernde Seite ungültig macht. (Siehe Parameter `/statfileslevel` des Konfigurationselements [Cache](dispatcher-configuration.md#main-pars_146_44_0010)) Die folgende Beispiel-HTTP-Anfragemeldung löscht und wiederholt `/content/geometrixx-outdoors/en.html page`:

```xml
POST /dispatcher/invalidate.cache HTTP/1.1  
CQ-Action: Activate  
Content-Type: text/plain   
CQ-Handle: /content/geometrixx-outdoors/en/men.html  
Content-Length: 36

/content/geometrixx-outdoors/en.html
```

### Beispiel-Servlet für das Leeren des Caches {#example-flush-servlet}

Mit dem folgenden Code wird ein Servlet implementiert, das eine Invalidierungsanfrage an den Dispatcher sendet. Das Servlet empfängt eine Meldung, die die Parameter `handle` und `page` enthält. Diese Parameter stellen den Wert des Headers `CQ-Handle` bereit und dementsprechend auch den Pfad zu der Datei, die erneut zwischengespeichert werden soll. Das Servlet verwendet die Werte, um die HTTP-Anfrage für den Dispatcher zu erstellen.

Wenn das Servlet für die Veröffentlichungsinstanz bereitgestellt wird, veranlasst die folgende URL den Dispatcher dazu, die Seite „/content/geometrixx-outdoors/en.html“ zu löschen und dann eine neue Kopie zwischenzuspeichern.

`10.36.79.223:4503/bin/flushcache/html?page=/content/geometrixx-outdoors/en.html&handle=/content/geometrixx-outdoors/en/men.html`

>[!NOTE]
>
>Dieses Beispiel-Servlet ist nicht sicher und dient nur dazu, die Verwendung der HTTP-Post-Anfragemeldung zu veranschaulichen. Ihre Lösung sollte den Zugriff auf das Servlet sicher machen.
>

```java
package com.adobe.example;

import org.apache.felix.scr.annotations.Component;
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.Property;

import org.apache.sling.api.SlingHttpServletRequest;
import org.apache.sling.api.SlingHttpServletResponse;
import org.apache.sling.api.servlets.SlingSafeMethodsServlet;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.apache.commons.httpclient.*;
import org.apache.commons.httpclient.methods.PostMethod;
import org.apache.commons.httpclient.methods.StringRequestEntity;

@Component(metatype=true)
@Service
public class Flushcache extends SlingSafeMethodsServlet {

 @Property(value="/bin/flushcache")
 static final String SERVLET_PATH="sling.servlet.paths";

 private Logger logger = LoggerFactory.getLogger(this.getClass());

 public void doGet(SlingHttpServletRequest request, SlingHttpServletResponse response) {
  try{ 
      //retrieve the request parameters
      String handle = request.getParameter("handle");
      String page = request.getParameter("page");

      //hard-coding connection properties is a bad practice, but is done here to simplify the example
      String server = "localhost"; 
      String uri = "/dispatcher/invalidate.cache";

      HttpClient client = new HttpClient();

      PostMethod post = new PostMethod("https://"+host+uri);
      post.setRequestHeader("CQ-Action", "Activate");
      post.setRequestHeader("CQ-Handle",handle);
   
      StringRequestEntity body = new StringRequestEntity(page,null,null);
      post.setRequestEntity(body);
      post.setRequestHeader("Content-length", String.valueOf(body.getContentLength()));
      client.executeMethod(post);
      post.releaseConnection();
      //log the results
      logger.info("result: " + post.getResponseBodyAsString());
      }
  }catch(Exception e){
      logger.error("Flushcache servlet exception: " + e.getMessage());
  }
 }
}
```
