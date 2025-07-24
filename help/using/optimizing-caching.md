---
title: Optimieren einer Website für Cache-Leistung
description: Sie erfahren, wie Sie Ihre Website einrichten, um die Vorteile der Zwischenspeicherung zu maximieren.
contentOwner: User
products: SG_EXPERIENCEMANAGER/DISPATCHER
topic-tags: dispatcher
content-type: reference
redirecttarget: https://helpx.adobe.com/de/experience-manager/6-4/sites/deploying/using/configuring-performance.html
index: y
internal: n
snippet: y
source-git-commit: c41b4026a64f9c90318e12de5397eb4c116056d9
workflow-type: tm+mt
source-wordcount: '1128'
ht-degree: 97%

---


# Optimieren einer Website für Cache-Leistung {#optimizing-a-website-for-cache-performance}

<!-- 

Comment Type: remark
Last Modified By: Silviu Raiman (raiman)
Last Modified Date: 2017-10-25T04:13:34.919-0400

<p>This is a redirect to /experience-manager/6-2/sites/deploying/using/configuring-performance.html</p>

 -->

>[!NOTE]
>
>Dispatcher-Versionen sind unabhängig von AEM. Möglicherweise wurden Sie auf diese Seite umgeleitet, da Sie einem Link zur Dispatcher-Dokumentation gefolgt sind. Dieser Link war in die Dokumentation für eine frühere AEM-Version eingebettet.

Der Dispatcher bietet verschiedene integrierte Mechanismen, mit denen Sie die Leistung optimieren können. In diesem Abschnitt erfahren Sie, wie Sie Ihre Website gestalten können, um die Vorteile der Zwischenspeicherung zu maximieren.

>[!NOTE]
>
>Vielleicht erinnern Sie sich daran, dass der Dispatcher den Cache auf einem standardmäßigen Webserver speichert. Dies bedeutet, dass Sie:
>
>* alles zwischenspeichern können, was als Seite gespeichert und mit einer URL abgerufen werden kann
>* keine anderen Elemente speichern können, z. B. HTTP-Header, Cookies, Sitzungs- und Formulardaten
>
>Allgemein beinhalten viele Caching-Strategien die Auswahl geeigneter URLs, ohne dabei von diesen zusätzlichen Daten abhängig zu sein.

## Verwenden einer konsistenten Seitencodierung {#using-consistent-page-encoding}

HTTP-Anfrage-Header werden nicht zwischengespeichert. Daher können Probleme auftreten, wenn Sie Seiten-Codierungsinformationen im Header speichern. In diesem Fall wird die Standardkodierung des Webservers für die Seite verwendet, wenn der Dispatcher eine Seite aus dem Cache bereitstellt. Es gibt zwei Möglichkeiten, dieses Problem zu vermeiden:

* Wenn Sie nur eine Codierung verwenden, stellen Sie sicher, dass die auf dem Webserver verwendete Codierung mit der Standardcodierung der AEM-Website übereinstimmt.
* Verwenden Sie zum Festlegen der Codierung einen `<META>`-Tag im HTML-`head`-Abschnitt wie im folgenden Beispiel:

```xml
        <META http-equiv="Content-Type" content="text/html; charset=EUC-JP">
```

## URL-Parameter vermeiden {#avoid-url-parameters}

Vermeiden Sie nach Möglichkeit URL-Parameter für Seiten, die Sie zwischenspeichern möchten. Wenn Sie beispielsweise über eine Bildergalerie verfügen, wird die folgende URL nie zwischengespeichert (es sei denn, der Dispatcher ist [entsprechend konfiguriert](dispatcher-configuration.md#main-pars_title_24)):

```xml
www.myCompany.com/pictures/gallery.html?event=christmas&amp;page=1
```

Sie können diese Parameter jedoch wie folgt in die Seiten-URL einfügen:

```xml
www.myCompany.com/pictures/gallery.christmas.1.html
```

>[!NOTE]
>
>Diese URL ruft dieselbe Seite und Vorlage auf wie „gallery.html“. In der Vorlagendefinition können Sie angeben, welches Skript die Seite rendert, oder Sie können dasselbe Skript für alle Seiten verwenden.

## Anpassen nach URL  {#customize-by-url}

Wenn Sie Benutzerinnen und Benutzern erlauben, die Schriftgröße (oder eine andere Layout-Anpassung) zu ändern, stellen Sie sicher, dass die verschiedenen Anpassungen in der URL berücksichtigt werden.

Beispielsweise werden Cookies nicht zwischengespeichert. Wenn Sie die Schriftgröße also in einem Cookie (oder einem ähnlichen Mechanismus) speichern, bleibt die Schriftgröße für die zwischengespeicherte Seite nicht erhalten. Daher gibt der Dispatcher Dokumente mit zufälliger Schriftgröße zurück.

Durch Einbeziehung der Schriftgröße in die URL als Selektor wird dieses Problem vermieden:

```xml
www.myCompany.com/news/main.large.html
```

>[!NOTE]
>
>Bei den meisten Layout-Aspekten ist es auch möglich, Stylesheets, Client-seitige Skripte oder beides zu verwenden. Mit jeweils guten Caching-Ergebnissen.
>
>Diese Methode ist auch für Druckversionen nützlich, für die Sie eine URL verwenden können, z. B.:
>
>`www.myCompany.com/news/main.print.html`
>
>Mithilfe des Skript-Globbings der Vorlagendefinition können Sie ein separates Skript angeben, das die Druckseiten rendert.

## Ungültigmachen von Bilddateien als Titel {#invalidating-image-files-used-as-titles}

Wenn Sie Seitentitel oder anderen Text als Bilder gerendert haben, speichern Sie die Dateien so, dass sie bei einer Inhaltsaktualisierung auf der Seite gelöscht werden:

1. Platzieren Sie die Bilddatei im selben Ordner wie die Seite.
1. Verwenden Sie das folgende Namensformat für die Bilddatei:

   `<page file name>.<image file name>`

Beispielsweise können Sie den Titel der Seite „myPage.html“ in der Datei „myPage.title.gif“ speichern. Diese Datei wird automatisch gelöscht, wenn die Seite aktualisiert wird, sodass jede Änderung am Seitentitel automatisch im Cache angezeigt wird.

>[!NOTE]
>
>Die Bilddatei ist nicht unbedingt in der AEM-Instanz vorhanden. Sie können ein Skript verwenden, das die Bilddatei dynamisch erstellt. Der Dispatcher speichert die Datei dann auf dem Webserver.

## Invalidierung der für die Navigation verwendeten Bilddateien {#invalidating-image-files-used-for-navigation}

Wenn Sie Bilder als Navigationseinträge verwenden, gehen Sie im Prinzip wie bei Titeln vor, das Verfahren ist nur ein wenig komplexer. Speichern Sie alle Navigationsbilder mit den Zielseiten. Wenn Sie zwei Bilder für „normal“ und „aktiv“ verwenden, können Sie die folgenden Skripte verwenden:

* Ein Skript, das die Seite wie gewohnt anzeigt.
* Ein Skript, das `.normal`-Anfragen verarbeitet und das normale Bild zurückgibt.
* Ein Skript, das `.active`-Anfragen verarbeitet und das aktivierte Bild zurückgibt.

Es ist wichtig, dass Sie diese Bilder mit demselben Namens-Handle wie die Seite erstellen, um sicherzustellen, dass durch eine Inhaltsaktualisierung diese Bilder und die Seite gelöscht werden.

Bei Seiten, die nicht geändert werden, bleiben die Bilder im Cache, obwohl die Seiten selbst automatisch ungültig gemacht werden.

## Personalisierung {#personalization}

Der Dispatcher kann keine personalisierten Daten zwischenspeichern. Sie sollten die Personalisierung daher nur bei Bedarf verwenden. Um zu veranschaulichen, warum:

* Wenn Sie eine frei anpassbare Startseite verwenden, muss diese Seite jedes Mal erstellt werden, wenn eine Benutzerin oder ein Benutzer sie anfordert.
* Wenn Sie dagegen zehn verschiedene Startseiten auswählen, können Sie jede dieser Startseiten zwischenspeichern und so die Leistung verbessern.

>[!NOTE]
>
>Wenn Sie jede Seite personalisieren (z. B. durch Einfügen des Benutzernamens in der Titelleiste), können Sie sie nicht zwischenspeichern. Dies kann die Leistung erheblich beeinträchtigen.
>
>Sollte dies aber erforderlich sein, können Sie wie folgt vorgehen:
>
>* Sie können die Seite mit iFrames aufteilen – in einen Teil, der für alle Benutzenden gleich ist, und einen Teil, der bei allen Seiten einer Person gleich ist. Diese beiden Teile können dann zwischengespeichert werden.
>* Sie können mit Client-seitigem JavaScript personalisierte Informationen anzeigen. Sie müssen jedoch sicherstellen, dass die Seite weiterhin richtig angezeigt wird, wenn jemand JavaScript deaktiviert.
>

## Sticky-Verbindungen {#sticky-connections}

[Sticky-Verbindungen](dispatcher.md#TheBenefitsofLoadBalancing) stellen sicher, dass die Dokumente für eine Benutzerin oder einen Benutzer alle auf demselben Server erstellt werden. Wenn eine Benutzerin oder ein Benutzer diesen Ordner verlässt und später zu ihm zurückkehrt, bleibt die Verbindung erhalten. Definieren Sie einen Ordner zum Speichern aller Dokumente, die Sticky-Verbindungen für die Website benötigen. Dieser sollte keine anderen Dokumente enthalten. Dies wirkt sich auf den Lastenausgleich aus, wenn Sie personalisierte Seiten und Sitzungsdaten verwenden.

## MIME-Typen {#mime-types}

Es gibt zwei Möglichkeiten, wie ein Browser den Typ einer Datei bestimmen kann:

1. Über ihre Erweiterung (wie .html, .gif und .jpg)
1. Über den MIME-Typ, den der Server mit der Datei sendet.

Für die meisten Dateien wird der MIME-Typ durch die Dateierweiterung angegeben:

1. Über ihre Erweiterung (wie .html, .gif und .jpg)
1. Über den MIME-Typ, den der Server mit der Datei sendet.

Wenn der Dateiname keine Erweiterung aufweist, wird er als Nur-Text angezeigt.

Der MIME-Typ ist ein Bestandteil des HTTP-Headers. Daher wird er nicht vom Dispatcher zwischengespeichert. Ihre AEM-Anwendung kann Dateien zurückgeben, die keine erkannte Dateierweiterung haben. Wenn die Dateien stattdessen vom MIME-Typ abhängen, werden diese Dateien möglicherweise falsch angezeigt.

Um sicherzustellen, dass Dateien ordnungsgemäß zwischengespeichert werden, befolgen Sie die folgenden Richtlinien:

* Stellen Sie sicher, dass Dateien immer die richtige Erweiterung haben.
* Verwenden Sie möglichst keine allgemeinen Dateibereitstellungsskripte mit URLs wie „download.jsp?file=2214“. Schreiben Sie das Skript so um, dass es URLs verwendet, die die Dateispezifikation enthalten. Für das vorherige Beispiel wäre dies `download.2214.pdf`.

