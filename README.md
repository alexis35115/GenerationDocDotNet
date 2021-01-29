# Comment produire une documentation à partir des commentaires dans le code C\#

Ce guide expliquera les différentes étapes afin de générer une documentation PDF à partir des commentaires en format XML dans du code C#.

## Contexte

Plusieurs outils permettent de générer une documentation statique en HTML à partir des commentaires en format XML dans notre code. Dans ce guide,l'outil [DocFX](https://dotnet.github.io/docfx/) sera utilisé, celui-ci permet de générer une documentation sous plusieurs formats.

## Installation de DocFX

DocFX permet de générer la documentation statique en format HTML à partir de commentaires en XML dans le code.

Il y a plusieurs façons d'installer DocFX. Celle que nous utiliserons est [intégrée dans Visual Studio (NuGet)](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#3-use-docfx-integrated-with-visual-studio) et facilitera notre travail (NuGet).

1. Créer un nouveau projet "C# Class Library".
2. Clique droit sur le projet dans l’Explorateur de solution -> "Manage NuGet Packages...".
3. Installer le package "DocFX.console", il s'ajoutera automatiquement au build target.
4. On peut maintenant Build notre projet. La documentation au format HTML sera générée dans le répertoire "_site"


On peut aussi installer DocFX comme [outil en ligne de commande](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), ça impliquera cependant un peu plus de configuration.
Il est également possible d'inclure la [génération sur un serveur de compilation](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#4-use-docfx-with-a-build-server), cette pratique est largement répandue dans le domaine.

## Installation de wkhtmltopdf

wkhtmltopdf permet de convertir le format HTML en PDF.

À partir d'une console PowerShell avec droits administrateur:
`choco install wkhtmltopdf -y`

## Étapes

### Étapes 1 - Documenter son code

Il faut savoir bien documenter son code! La qualité des commentaires augmentera l'utilité du site généré. Voici [quelques directives](https://docs.microsoft.com/fr-ca/dotnet/csharp/codedoc) à suivre pour bien documenter son code en .Net.

### Étapes 2 - Générer la documentation PDF avec DocFX

Cette procédure expliquera comment générer un fichier PDF avec notre documentation.

1. Configurez la génération PDF dans le fichiers docfx.json en ajoutant sous la configuration "build", la configuration ici-bas :

```json
 "pdf": {
    "content": [
      {
        "files": [
          "api/**.yml"
        ],
        "exclude": [
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": [
          "articles/**.md",
          "articles/**/toc.yml",
          "toc.yml",
          "*.md",
          "pdf/*"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**",
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": "pdf/toc.yml"
      }
    ],
    "resource": [
      {
        "files": [
          "images/**"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**"
        ]
      }
    ],
    "overwrite": [
      {
        "files": [
          "apidoc/**.md"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**"
        ]
      }
    ],
    "wkhtmltopdf": {
      "additionalArguments": "--enable-local-file-access"
    },
    "dest": "_site_pdf"
  }
```

2. À la racine du projet, créer un répertoire "pdf"
3. Dans ce dernier, créer un fichier "toc.yml" et copier les lignes suivantes :

__Prendre note que "name" et "href" doivent être alignés__

```yml
- name: Articles
  href: ../articles/toc.yml
- name: Api Documentation
  href: ../api/toc.yml
```

4. Compilez votre projet pour générer la documentation sous forme de PDF. Il apparaitra dans le répertoire "_site_pdf"


Voici un exemple de fichier "docfx.json"

```json
{
  "metadata": [
    {
      "src": [
        {
          "files": [
            "**.csproj"
          ],
          "src": ""
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
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": [
          "articles/**.md",
          "articles/**/toc.yml",
          "toc.yml",
          "*.md",
          "pdf/*"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**",
          "**/toc.yml",
          "**/toc.md"
        ]
      },
      {
        "files": "pdf/toc.yml"
      }
    ],
    "resource": [
      {
        "files": [
          "images/**"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**"
        ]
      }
    ],
    "overwrite": [
      {
        "files": [
          "apidoc/**.md"
        ],
        "exclude": [
          "**/bin/**",
          "**/obj/**",
          "_site_pdf/**"
        ]
      }
    ],
    "wkhtmltopdf": {
      "additionalArguments": "--enable-local-file-access"
    },
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