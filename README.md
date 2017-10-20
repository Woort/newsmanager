News-Manager
============

![Screenshot](https://raw.githubusercontent.com/FriendsOfREDAXO/newsmanager/assets/screenshot.png)

Dieses Addon stellt eine einfache Newsverwaltung bereit. Dabei werden die Beiträge in einer eigenen Tabelle abgelegt.

## Beschreibung

Die Kernfunktion ist die Verwaltung von Newsartikeln. Man kann Kategorien festlegen und die Artikel dann einer oder mehreren Kategorien zuordenen.

Für eine einfache Ausgabe der Artikel und Artikellisten sind einige Funktionen vorhanden. Man kann aber die Ausgabe auch über Datenbankabfragen realisieren.

Alle zukünftige Funktionen werden über Plugins eingebunden. Das erste Plugin realisiert eine Kommentarfunktioalität.

**Derzeitige Funktionen:**

* Kategorien
* Mehrsprachigkeit
* RSS Feed
* Kommentare (via Plugin)

### Installation

Einfach das Addon nach /redaxo/src/addons/ kopieren und im Addons Bereich installieren.

**Das Addon benötigt folgende Addons:**

* url Addon (für "sprechende" URLs)
* redactor2 Addon (optional, macht aber Sinn wenn man Richtext im Artikel verwenden will).

Das Addon enthält eine Einstellungsseite. Hier sollten Sie die Kategorie auswählen, in welcher im Startartikel die Artikelliste und die Artikelansicht ausgegben wird.
Beim Klick auf "Einstellungen speichern" wird (falls vorhanden) ein Profil für das redactor2 Addon sowie die Einstellungen für das url Addon angelegt.

### Template anpassen

Man kann natürlich einfach eine entsprechende Datenbank Abfrage machen und sich selbst um die Ausgabe kümmern. Wie das geht, kann man in der REDAXO Doku nachlesen.

Es gibt aber auch Funktionen, die sich um die Ausgabe kümmern.
Hier ein paar Beispiele für die Verwendung dieser Klassenfunktionen (Listen- und Singleansicht in einem einzigen Template):

**Headerbereich:**

```php
// Ohne Kommentarplugin:
// $newsmanager = new NewsManager();

// Mit Kommentarplugin:
$newsmanager = new NewsManagerWithComments();

$news_id = $newsmanager->getNewsIdParameter();

if ($news_id) {

    // Artikel-Ansicht
    
    $article_post = $newsmanager->getArticleById($news_id);
    
    echo $article_post->getTitleTag($this->getValue('article_id'));
    echo $article_post->getDescriptionTag();
    echo $article_post->getCanonicalUrlTag($this->getValue('article_id'));
    echo $article_post->getHrefLangTag ($article_post->getId());

} else {

    // Artikel-Listenansicht
    
    $seo = new rex_yrewrite_seo();
    
    echo $seo->getTitleTag();
    echo $seo->getDescriptionTag();
    echo $seo->getRobotsTag();
    echo $seo->getHreflangTags();
    echo $seo->getCanonicalUrlTag();

}
```

**RSS Link** (falls gewünscht)

```php
echo $newsmanager->getRssHeaderLink();
```

**Artikel-Ansicht und Artikel-Listenansicht**

```php
if ($news_id) {

    // Artikel-Ansicht
    
    echo $newsmanager->printSingleView($article_post);
    echo $newsmanager->getCommentList($article_post->getPid());
    echo $newsmanager->getCommentForm($article_post->getPid());

} else {

    // Artikel-Listenansicht
    
    echo '  <h1>' . $this->getValue("name") . '</h1>';
    
    // Ausgabe 10 Artikel, alle weiteren paginiert
    
    echo $newsmanager->printListView($this->getValue('article_id'), 10);

}
```

Nützt man das Comments-Plugin muss noch das nötige JavaScript hinzugefügt werden:

```php
echo $newsmanager->getCommentJavaScript();
```

**Kategorie Menü**

```php
echo $newsmanager->printCategoryMenu();
```

Den Quellcode für die Ausgabe kann man auch anpassen.
Es gibt dafür sog. Views, also HTML/PHP Schnipsel die in /redaxo/data/addons/newsmanager/views/ bzw. für die Kommentare
unter /redaxo/data/addons/newsmanager/views/comments/views/ abgelegt sind.
