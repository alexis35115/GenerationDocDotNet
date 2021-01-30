# Comment produire une documentation à partir des commentaires dans le code C\#

Ce guide expliquera les différentes étapes afin de générer une documentation PDF à partir des commentaires en format XML dans du code C#.

## Contexte

Plusieurs outils permettent de générer une documentation statique en HTML à partir des commentaires en format XML dans notre code. Dans ce guide,l'outil [DocFX](https://dotnet.github.io/docfx/) sera utilisé, celui-ci permet de générer une documentation sous plusieurs formats.

## Installation de DocFX

Voici les commandes à exécuter pour installer les modules requis (à partir d'une console PowerShell avec droits administrateur)
1. `choco install docfx -y` DocFX permet de générer la documentation statique en format HTML à partir de commentaires en XML dans le code
2. `choco install wkhtmltopdf -y` wkhtmltopdf permet de convertir le format HTML en PDF

Il est également possible d'inclure la [génération sur un serveur de compilation](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#4-use-docfx-with-a-build-server), cette pratique est largement répandue dans le domaine.

## Étapes

### Étapes 1 - documenter son code

Il faut savoir bien documenter son code! La qualité des commentaires augmentera l'utilité du site généré. Voici [quelques directives](https://docs.microsoft.com/fr-ca/dotnet/csharp/codedoc) à suivre pour bien documenter son code en .Net.

### Étape 2 -  activer la génération du fichier XML qui contient les commentaires

Pour activer la génération du site statique en HTML, double-cliquer sur le fichier ".csproj" et ajouter la balise :

``` xml
  <PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
  </PropertyGroup>
```

### Étapes 3 - générer la documentation PDF avec DocFX

Cette procédure expliquera comment générer un fichier PDF avec notre documentation.

1. Ouvrir une invite de commandes Powershell à partir à partir de la racine de la solution
2. Entrez la commande `docfx init -q`
3. Naviguer dans le répertoire "docfx_project"
4. Ouvrir le fichier "docfx.json" avec un éditeur de texte
5. Remplacer la section "metadata" avec l'exemple ici-bas en __changant le nom des projets__

```json
"metadata": [
    {
      "src": [
        {
          "src": "../",
          "files": [
            "NOM_PROJET1/**.csproj",
            "NOM_PROJET2/**.csproj"
          ],
          "exclude": [
            "**/bin/**",
            "**/obj/**",
            "_site/**"
          ]
        }
      ],
      "dest": "api",
      "disableGitFeatures": false,
      "disableDefaultFilter": false
    }
  ]
```

6. Configurez la génération PDF en ajoutant sous la configuration "build", la configuration ici-bas :

```json
 "pdf": {
    "content": [
      {
        "files": [
          "api/**.yml"
        ],
        "exclude": [
          "**/toc.yml"
        ]
      },
      {
        "files": [ "articles/**/*.md", "*.md", "toc.yml"],
        "exclude": [
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": [ "pdf/toc.yml"]
      }
    ],
    "resource": [
      {
        "files": [ "articles/images/**"]
      }
    ],
    "overwrite": "specs/*.md",
    "dest": "_site_pdf"
  }
```

7. À la racine du répertoire "docfx_project" créez un répertoire "pdf"
8. Créez un fichier "toc.yml" et copier les lignes suivantes :

__Prendre note que "name" et "href" doivent être alignés__

```yml
- name: Articles
  href: ../articles/toc.yml
- name: Api Documentation
  href: ../api/toc.yml
```

9. Pour générer la documentation PDF entrez la commande "docfx docfx.json" à la racine du répertoire "docfx_project" et le fichier PDF sera généré sous "_site_pdf"
10. Pour générer le site web statique, entrez la commande "docfx docfx.json --serve" et naviguez à l'adresse "<http://localhost:8080/>"

Voici un exemple de fichier "docfx.json"

```json
{
  "metadata": [
    {
      "src": [
        {
          "src": "../",
          "files": [
            "AutreProjetPourGenerationDocumentation/**.csproj",
            "ExempleGenerationDocumentation/**.csproj"
          ],
          "exclude": [
            "**/bin/**",
            "**/obj/**",
            "_site/**"
          ]
        }
      ],
      "dest": "api",
      "disableGitFeatures": false,
      "disableDefaultFilter": false
    }
  ],
  "build": {
    "content": [
      {
        "files": [
          "api/**.yml",
          "api/index.md"
        ]
      },
      {
        "files": [
          "articles/**.md",
          "articles/**/toc.yml",
          "toc.yml",
          "*.md"
        ]
      }
    ],
    "resource": [
      {
        "files": [
          "images/**"
        ]
      }
    ],
    "overwrite": [
      {
        "files": [
          "apidoc/**.md"
        ],
        "exclude": [
          "obj/**",
          "_site/**"
        ]
      }
    ],
    "dest": "_site",
    "globalMetadataFiles": [],
    "fileMetadataFiles": [],
    "template": [
      "default"
    ],
    "postProcessors": [],
    "markdownEngineName": "markdig",
    "noLangKeyword": false,
    "keepFileLink": false,
    "cleanupCacheHistory": false,
    "disableGitFeatures": false
  },
  "pdf": {
    "content": [
      {
        "files": [
          "api/**.yml"
        ],
        "exclude": [
          "**/toc.yml"
        ]
      },
      {
        "files": [ "articles/**/*.md", "*.md", "toc.yml"],
        "exclude": [
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": [ "pdf/toc.yml"]
      }
    ],
    "resource": [
      {
        "files": [ "articles/images/**"]
      }
    ],
    "overwrite": "specs/*.md",
    "dest": "_site_pdf"
  }
}
```

Pour plus d'informations, lire la [documentation officielle](https://dotnet.github.io/docfx/tutorial/docfx.exe_user_manual.html).

## Références

<https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html>

<https://dotnet.github.io/docfx/tutorial/walkthrough/walkthrough_generate_pdf.html>

<https://stackoverflow.com/questions/56601206/generating-pdf-documentation-from-visual-studio-with-docfx-throws-error-wkhtml>

<https://developers.de/2018/01/11/publish-xml-documentation-asp-net-core/>

<https://stackoverflow.com/questions/47115877/how-to-generate-xml-documentation-for-csproj-with-multiple-targets>