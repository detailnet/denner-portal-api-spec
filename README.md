# Denner Portal 2.0 API Spec

## Terms and translations
Consider the following terms:

| DE          | EN          |
|-------------|-------------|
| Filiale     | Store       |
| Artikel     | Article     |
| Werbemittel | Publication |
| Aktion      | Promotion   |
| bewerben    | advertise   |

## Data and resources
The Denner Portal provides mostly advertising related data.

### Stores

* `/stores` (Filialen, [example](examples/stores.json))
* `/store-channels` (Filialkanäle, [example](examples/store-channels.json))
* `/store-services` (Filialangebote, [example](examples/store-services.json))
* `/screen-stores` (Screen-Filialen, [example](examples/screen-stores.json))

### Articles/Promotions

* `/article-groups` (Warengruppen, [example](examples/article-groups.json))
* `/articles` (Artikel, [example](examples/articles.json))
* `/shop-wines` (Wineshop-Weine)
* `/mobile-wines` (Mobile-App-Weine)
* `/online-groups` (Internet-Sortimente, [example](examples/online-groups.json))
* `/online-specials` (Online-Specials, [example](examples/online-specials.json))
* `/advertised-articles` (beworbene Artikel)
* `/promotion-types` (Aktionstypen, [example](examples/promotion-types.json))

### Advertising

#### Online
* `/online-publications` (Online-Werbemittel, [example](examples/online-publications.json))
* `/online-publication-articles/{article_id}` (Online-Werbemittel-Artikel, [example](examples/online-publication-article.json))
* `/online-filters` (Angebotsfilter, [example](examples/online-filters.json))

#### Screen
* `/screen-publications` (Screen-Werbemittel, [example](examples/screen-publications.json))

#### Print
* `/print-publications` (Print-Werbemittel, [example](examples/print-publications.json))
* `/print-publications/{publication_id]` (Print-Werbemittel, [example](examples/print-publication.json))
* `/print-layouts` (Print-Layoutarten, [example](examples/print-layouts.json))
* `/print-formats` (Print-Formate und -Vorlagen, [example](examples/print-formats.json))
* `/print-schemes` (Print-Werbemittelschemas, [example](examples/print-schemes.json))

### Appraisals
* `/appraisals` (Weinbeurteilungen, [example](examples/appraisals.json))
* `/appraisals/{appraisal_id}` (Weinbeurteilung, [example](examples/appraisal.json))
* `/appraisal-statistic/{date}` (Statistiken zu Weinbewertungen und -beurteilungen, [example](examples/appraisal-statistic.json))
* `/ratings` (Weinbewertungen, [example](examples/ratings.json))
* `/ratings/{article_id}` (Weinbewertung, [example](examples/rating.json))
* `/sweepstake-participants` (Verlosungsteilnehmer, [example](examples/sweepstake-participants.json))

## Building

### Protobox
To build the specification we're using [swagger-codegen](https://github.com/swagger-api/swagger-codegen).

Run the following commands in [Protobox](https://bitbucket.org/detailnet/protobox) to install it (and it's dependencies):

        sudo apt-get update
        sudo apt-get install maven
        sudo apt-get install openjdk-7-jdk
        git clone git@github.com:swagger-api/swagger-codegen.git
        cd swagger-codegen
        mvn package
        
If Maven reports loo low version on packaging, follow [those instructions](https://linuxize.com/post/how-to-install-apache-maven-on-debian-9/) to upgrade to latest version.        
        
You should also install the JSON processor utility for further data manipulation:
        
        sudo apt-get install npm
        sudo npm install -g json
  

#### JSON
Once installed, `swagger.json` can be generated as follows:

        java -jar modules/swagger-codegen-cli/target/swagger-codegen-cli.jar generate \
            -i ../denner-portal-api-spec/src/swagger.yml \
            -l swagger \
            -o ../denner-portal-api-spec/build/swagger
        
The file will be located at `build/swagger/swagger.json`.

To filter out examples and descriptions execute following (JSON processor needed):
 
        json -e '
          function dropRecursive(obj, objName) {
            if (objName != "properties") {
              delete obj.examples;
              delete obj.example;
              delete obj.description;
              delete obj.summary;
            }
            
            for (var key in obj) {
              if (obj[key] && typeof obj[key] === "object") { 
                dropRecursive(obj[key], key);
              }
            }
          }
          
          dropRecursive(this, 'this');
        ' < ../denner-portal-api-spec/build/swagger/swagger.json > ../denner-portal-api-spec/build/swagger/swagger.no_texts.json

The file will be located at `build/swagger/swagger.no_texts.json`.

To compress all your JSON data execute following (JSON processor needed):

        for f in `ls ../denner-portal-api-spec/build/swagger/*.json | grep -v "compressed"`
        do 
          json -o json-0 < $f > "${f%.json}.compressed.json"
        done

#### HTML
You can also generate a static HTML page:

        java -jar modules/swagger-codegen-cli/target/swagger-codegen-cli.jar generate \
            -i ../denner-portal-api-spec/src/swagger.yml \
            -l html \
            -o ../denner-portal-api-spec/build/html
            
The file will be located at `build/html/index.html`.
