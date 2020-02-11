---
title: 'Tutorial: usando o recurso de migrações – ASP.NET MVC com EF Core'
description: Neste tutorial, você começa a usar o recurso de migrações do EF Core para gerenciar alterações do modelo de dados em um aplicativo ASP.NET Core MVC.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 9f3e3b29d155f1024aef530bf9c55efa57d4546a
ms.sourcegitcommit: 0b0e485a8a6dfcc65a7a58b365622b3839f4d624
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76928396"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Tutorial: usando o recurso de migrações – ASP.NET MVC com EF Core

Neste tutorial, você começa usando o recurso de migrações do EF Core para o gerenciamento de alterações do modelo de dados. Em tutoriais seguintes, você adicionará mais migrações conforme você alterar o modelo de dados.

Neste tutorial, você:

> [!div class="checklist"]
> * Aprenderá sobre migrações
> * Alterar a cadeia de conexão
> * Criar uma migração inicial
> * Examinará os métodos Up e Down
> * Aprenderá sobre o instantâneo do modelo de dados
> * Aplicar a migração

## <a name="prerequisites"></a>Pré-requisitos

* [Classificação, filtragem e paginação](sort-filter-page.md)

## <a name="about-migrations"></a>Sobre migrações

Quando você desenvolve um novo aplicativo, o modelo de dados é alterado com frequência e, sempre que o modelo é alterado, ele fica fora de sincronia com o banco de dados. Você começou estes tutoriais configurando o Entity Framework para criar o banco de dados, caso ele não exista. Em seguida, sempre que você alterar o modelo de dados – adicionar, remover, alterar classes de entidade ou alterar a classe DbContext –, poderá excluir o banco de dados e o EF criará um novo que corresponde ao modelo e o propagará com os dados de teste.

Esse método de manter o banco de dados em sincronia com o modelo de dados funciona bem até que você implante o aplicativo em produção. Quando o aplicativo é executado em produção, ele normalmente armazena os dados que você deseja manter, e você não quer perder tudo sempre que fizer uma alteração, como a adição de uma nova coluna. O recurso Migrações do EF Core resolve esse problema, permitindo que o EF atualize o esquema de banco de dados em vez de criar um novo banco de dados.

Para trabalhar com migrações, você pode usar o **console do Gerenciador de pacotes** (PMC) ou a CLI.  Esses tutoriais mostram como usar comandos da CLI. Encontre informações sobre o PMC no [final deste tutorial](#pmc).

## <a name="change-the-connection-string"></a>Alterar a cadeia de conexão

No arquivo *appsettings.json*, altere o nome do banco de dados na cadeia de conexão para ContosoUniversity2 ou outro nome que você ainda não usou no computador que está sendo usado.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Essa alteração configura o projeto, de modo que a primeira migração crie um novo banco de dados. Isso não é necessário para começar as migrações, mas você verá mais tarde o motivo pelo qual essa é uma boa ideia.

> [!NOTE]
> Como alternativa à alteração do nome do banco de dados, você pode excluir o banco de dados. Use o **SSOX** (Pesquisador de Objetos do SQL Server) ou o comando `database drop` da CLI:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> A seção a seguir explica como executar comandos da CLI.

## <a name="create-an-initial-migration"></a>Criar uma migração inicial

Salve as alterações e compile o projeto. Em seguida, abra uma janela Comando e navegue para a pasta do projeto. Esta é uma maneira rápida de fazer isso:

* No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Abrir Pasta no Explorador de Arquivos** no menu de contexto.

  ![Abrir no item de menu do Explorador de Arquivos](migrations/_static/open-in-file-explorer.png)

* Insira "cmd" na barra de endereços e pressione Enter.

  ![Abrir a janela Comando](migrations/_static/open-command-window.png)

Insira o seguinte comando na janela de comando:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Você verá uma saída semelhante à seguinte na janela Comando:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Se você receber uma mensagem de erro *Nenhum comando "dotnet-ef" executável correspondente encontrado*, consulte [esta postagem no blog](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) para ajudar a solucionar o problema.

Se você vir uma mensagem de erro "*não é possível acessar o arquivo... ContosoUniversity. dll porque ele está sendo usado por outro processo.* ", localize o ícone de IIS Express na bandeja do sistema do Windows e clique com o botão direito do mouse nele e clique em **ContosoUniversity > parar site**.

## <a name="examine-up-and-down-methods"></a>Examinará os métodos Up e Down

Quando você executou o comando `migrations add`, o EF gerou o código que criará o banco de dados do zero. Esse código está localizado na pasta *Migrations*, no arquivo chamado *\<timestamp>_InitialCreate.cs*. O método `Up` da classe `InitialCreate` cria as tabelas de banco de dados que correspondem aos conjuntos de entidades do modelo de dados, e o método `Down` exclui-as, conforme mostrado no exemplo a seguir.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

As migrações chamam o método `Up` para implementar as alterações do modelo de dados para uma migração. Quando você insere um comando para reverter a atualização, as Migrações chamam o método `Down`.

Esse código destina-se à migração inicial que foi criada quando você inseriu o comando `migrations add InitialCreate`. O parâmetro de nome da migração ("InitialCreate" no exemplo) é usado para o nome do arquivo e pode ser o que você desejar. É melhor escolher uma palavra ou frase que resume o que está sendo feito na migração. Por exemplo, você pode nomear uma migração posterior "AddDepartmentTable".

Se você criou a migração inicial quando o banco de dados já existia, o código de criação de banco de dados é gerado, mas ele não precisa ser executado porque o banco de dados já corresponde ao modelo de dados. Quando você implantar o aplicativo em outro ambiente no qual o banco de dados ainda não existe, esse código será executado para criar o banco de dados; portanto, é uma boa ideia testá-lo primeiro. É por isso que você alterou o nome do banco de dados na cadeia de conexão anteriormente – para que as migrações possam criar um novo do zero.

## <a name="the-data-model-snapshot"></a>O instantâneo do modelo de dados

As migrações criam um *instantâneo* do esquema de banco de dados atual em *Migrations/SchoolContextModelSnapshot.cs*. Quando você adiciona uma migração, o EF determina o que foi alterado, comparando o modelo de dados com o arquivo de instantâneo.

Use o comando [dotnet EF migrações remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) para remover uma migração. `dotnet ef migrations remove` exclui a migração e garante que o instantâneo seja redefinido corretamente. Se `dotnet ef migrations remove` falhar, use `dotnet ef migrations remove -v` para obter mais informações sobre a falha.

Confira [Migrações do EF Core em ambientes de equipe](/ef/core/managing-schemas/migrations/teams) para obter mais informações de como o arquivo de instantâneo é usado.

## <a name="apply-the-migration"></a>Aplicar a migração

Na janela Comando, insira o comando a seguir para criar o banco de dados e tabelas nele.

```dotnetcli
dotnet ef database update
```

A saída do comando é semelhante ao comando `migrations add`, exceto que os logs para os comandos SQL que configuram o banco de dados são exibidos. A maioria dos logs é omitida na seguinte saída de exemplo. Se você preferir não ver esse nível de detalhe em mensagens de log, altere o nível de log no arquivo *appsettings.Development.json*. Para obter mais informações, consulte <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

Use o **Pesquisador de Objetos do SQL Server** para inspecionar o banco de dados como você fez no primeiro tutorial.  Você observará a adição de uma tabela \_\_EFMigrationsHistory que controla quais migrações foram aplicadas ao banco de dados. Exiba os dados dessa tabela e você verá uma linha para a primeira migração. (O último log no exemplo de saída da CLI anterior mostra a instrução INSERT que cria essa linha.)

Execute o aplicativo para verificar se tudo ainda funciona como antes.

![Página Índice de Alunos](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Comparar CLI e PMC

As ferramentas do EF para gerenciamento de migrações estão disponíveis por meio dos comandos da CLI do .NET Core ou de cmdlets do PowerShell na janela **PMC** (Console do Gerenciador de Pacotes) do Visual Studio. Este tutorial mostra como usar a CLI, mas você poderá usar o PMC se preferir.

Os comandos do EF para os comandos do PMC estão no pacote [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools). Esse pacote está incluído no [metapacote Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), portanto você não precisa adicionar uma referência de pacote se o aplicativo tem uma referência de pacote ao `Microsoft.AspNetCore.App`.

**Importante:** esse não é o mesmo pacote que é instalado para a CLI com a edição do arquivo *.csproj*. O nome deste termina com `Tools`, ao contrário do nome do pacote da CLI que termina com `Tools.DotNet`.

Para obter mais informações sobre os comandos da CLI, consulte [CLI do .NET Core](/ef/core/miscellaneous/cli/dotnet).

Para obter mais informações sobre os comandos do PMC, consulte [Console do Gerenciador de Pacotes (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Obter o código

[Baixe ou exiba o aplicativo concluído.](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Próximas etapas

Neste tutorial, você:

> [!div class="checklist"]
> * Aprendeu sobre migrações
> * Aprendeu sobre pacotes de migração do NuGet
> * Alterou a cadeia de conexão
> * Criou uma migração inicial
> * Examinou os métodos Up e Down
> * Aprendeu sobre o instantâneo do modelo de dados
> * Aplicou a migração

Vá para o próximo tutorial para começar a examinar tópicos mais avançados sobre a expansão do modelo de dados. Ao longo do processo, você criará e aplicará migrações adicionais.

> [!div class="nextstepaction"]
> [Criar e aplicar migrações adicionais](complex-data-model.md)
