---
title: Host da Web do ASP.NET Core
author: guardrex
description: Saiba mais sobre o host da Web no ASP.NET Core, que é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.
manager: wpickett
ms.author: riande
ms.custom: mvc
ms.date: 05/16/2018
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: fundamentals/host/web-host
ms.openlocfilehash: ced2a766359894b9b83164c12a3ab69aa13c93a0
ms.sourcegitcommit: a66f38071e13685bbe59d48d22aa141ac702b432
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="c4542-103">Host da Web do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c4542-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="c4542-104">Por [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c4542-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c4542-105">Aplicativos ASP.NET Core configuram e inicializam um *host*.</span><span class="sxs-lookup"><span data-stu-id="c4542-105">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="c4542-106">O host é responsável pelo gerenciamento de tempo de vida e pela inicialização do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-106">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="c4542-107">No mínimo, o host configura um servidor e um pipeline de processamento de solicitações.</span><span class="sxs-lookup"><span data-stu-id="c4542-107">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="c4542-108">Este tópico aborda o Host da Web do ASP.NET Core ([WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)), que é útil para hospedagem de aplicativos Web.</span><span class="sxs-lookup"><span data-stu-id="c4542-108">This topic covers the ASP.NET Core Web Host ([WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder)), which is useful for hosting web apps.</span></span> <span data-ttu-id="c4542-109">Para cobertura do Host Genérico .NET ([HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder)), veja o tópico [Host Genérico](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="c4542-109">For coverage of the .NET Generic Host ([HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder)), see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="set-up-a-host"></a><span data-ttu-id="c4542-110">Configurar um host</span><span class="sxs-lookup"><span data-stu-id="c4542-110">Set up a host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-111">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-111">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x/)

<span data-ttu-id="c4542-112">Crie um host usando uma instância de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="c4542-112">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="c4542-113">Normalmente, isso é feito no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="c4542-113">This is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="c4542-114">Em modelos de projeto, `Main` está localizado em *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="c4542-114">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="c4542-115">Um *Program.cs* típico chama [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) para começar a configurar um host:</span><span class="sxs-lookup"><span data-stu-id="c4542-115">A typical *Program.cs* calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="c4542-116">`CreateDefaultBuilder` executa as seguintes tarefas:</span><span class="sxs-lookup"><span data-stu-id="c4542-116">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="c4542-117">Configura o [Kestrel](xref:fundamentals/servers/kestrel) como o servidor Web.</span><span class="sxs-lookup"><span data-stu-id="c4542-117">Configures [Kestrel](xref:fundamentals/servers/kestrel) as the web server.</span></span> <span data-ttu-id="c4542-118">Para conhecer as opções padrão do Kestrel, consulte [Implementação do servidor Web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span><span class="sxs-lookup"><span data-stu-id="c4542-118">For the Kestrel default options, see [the Kestrel options section of Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel#kestrel-options).</span></span>
* <span data-ttu-id="c4542-119">Define a raiz do conteúdo como o caminho retornado por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span><span class="sxs-lookup"><span data-stu-id="c4542-119">Sets the content root to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="c4542-120">Carrega configurações opcionais de:</span><span class="sxs-lookup"><span data-stu-id="c4542-120">Loads optional configuration from:</span></span>
  * <span data-ttu-id="c4542-121">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c4542-121">*appsettings.json*.</span></span>
  * <span data-ttu-id="c4542-122">*appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c4542-122">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="c4542-123">[Segredos do usuário](xref:security/app-secrets) quando o aplicativo é executado no ambiente `Development`.</span><span class="sxs-lookup"><span data-stu-id="c4542-123">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
  * <span data-ttu-id="c4542-124">Variáveis de ambiente.</span><span class="sxs-lookup"><span data-stu-id="c4542-124">Environment variables.</span></span>
  * <span data-ttu-id="c4542-125">Argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c4542-125">Command-line arguments.</span></span>
* <span data-ttu-id="c4542-126">Configura o [registro em log](xref:fundamentals/logging/index) para a saída do console e de depuração.</span><span class="sxs-lookup"><span data-stu-id="c4542-126">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="c4542-127">O registro em log inclui regras de [filtragem de log](xref:fundamentals/logging/index#log-filtering) especificadas em uma seção de configuração de registro em log de um arquivo *appsettings.json* ou *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="c4542-127">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="c4542-128">Quando executado por trás do IIS, permite a [integração de IIS](xref:host-and-deploy/iis/index).</span><span class="sxs-lookup"><span data-stu-id="c4542-128">When running behind IIS, enables [IIS integration](xref:host-and-deploy/iis/index).</span></span> <span data-ttu-id="c4542-129">Configura o caminho base e a porta que o servidor escuta ao usar o [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c4542-129">Configures the base path and port the server listens on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module).</span></span> <span data-ttu-id="c4542-130">O módulo cria um proxy reverso entre o IIS e o Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c4542-130">The module creates a reverse proxy between IIS and Kestrel.</span></span> <span data-ttu-id="c4542-131">Também configura o aplicativo para [capturar erros de inicialização](#capture-startup-errors).</span><span class="sxs-lookup"><span data-stu-id="c4542-131">Also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="c4542-132">Para conhecer as opções padrão do IIS, consulte [a seção sobre as opções do IIS para Hospedar o ASP.NET Core no Windows com o IIS](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="c4542-132">For the IIS default options, see [the IIS options section of Host ASP.NET Core on Windows with IIS](xref:host-and-deploy/iis/index#iis-options).</span></span>
* <span data-ttu-id="c4542-133">Definirá [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` se o ambiente do aplicativo for de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c4542-133">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="c4542-134">Para obter mais informações, confira [Validação de escopo](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="c4542-134">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="c4542-135">A *raiz do conteúdo* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="c4542-135">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="c4542-136">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="c4542-136">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="c4542-137">Esse é o padrão usado no [Visual Studio](https://www.visualstudio.com/) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="c4542-137">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="c4542-138">Para obter mais informações sobre a configuração de aplicativos, consulte [Configuração no ASP.NET Core](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="c4542-138">For more information on app configuration, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

> [!NOTE]
> <span data-ttu-id="c4542-139">Como uma alternativa ao uso do método `CreateDefaultBuilder` estático, criar um host de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) é uma abordagem compatível com o ASP.NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="c4542-139">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span> <span data-ttu-id="c4542-140">Para obter mais informações, consulte a guia do ASP.NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="c4542-140">For more information, see the ASP.NET Core 1.x tab.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-141">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-141">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x/)

<span data-ttu-id="c4542-142">Crie um host usando uma instância de [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="c4542-142">Create a host using an instance of [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder).</span></span> <span data-ttu-id="c4542-143">A criação de um host normalmente é feita no ponto de entrada do aplicativo, o método `Main`.</span><span class="sxs-lookup"><span data-stu-id="c4542-143">Creating a host is typically performed in the app's entry point, the `Main` method.</span></span> <span data-ttu-id="c4542-144">Em modelos de projeto, `Main` está localizado em *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="c4542-144">In the project templates, `Main` is located in *Program.cs*:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .Build();
}
```

<span data-ttu-id="c4542-145">`WebHostBuilder` requer um [servidor que implementa IServer](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="c4542-145">`WebHostBuilder` requires a [server that implements IServer](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="c4542-146">Os servidores internos são [Kestrel](xref:fundamentals/servers/kestrel) e [HTTP.sys](xref:fundamentals/servers/httpsys) (antes do lançamento do ASP.NET Core 2.0, HTTP.sys era chamado de [WebListener](xref:fundamentals/servers/weblistener)).</span><span class="sxs-lookup"><span data-stu-id="c4542-146">The built-in servers are [Kestrel](xref:fundamentals/servers/kestrel) and [HTTP.sys](xref:fundamentals/servers/httpsys) (prior to the release of ASP.NET Core 2.0, HTTP.sys was called [WebListener](xref:fundamentals/servers/weblistener)).</span></span> <span data-ttu-id="c4542-147">Neste exemplo, o [método de extensão UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) especifica o servidor Kestrel.</span><span class="sxs-lookup"><span data-stu-id="c4542-147">In this example, the [UseKestrel extension method](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) specifies the Kestrel server.</span></span>

<span data-ttu-id="c4542-148">A *raiz do conteúdo* determina onde o host procura por arquivos de conteúdo, como arquivos de exibição do MVC.</span><span class="sxs-lookup"><span data-stu-id="c4542-148">The *content root* determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="c4542-149">A raiz de conteúdo padrão é obtida para `UseContentRoot` por [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span><span class="sxs-lookup"><span data-stu-id="c4542-149">The default content root is obtained for `UseContentRoot` by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1).</span></span> <span data-ttu-id="c4542-150">Quando o aplicativo é iniciado na pasta raiz do projeto, essa pasta é usada como a raiz do conteúdo.</span><span class="sxs-lookup"><span data-stu-id="c4542-150">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="c4542-151">Esse é o padrão usado no [Visual Studio](https://www.visualstudio.com/) e nos [novos modelos dotnet](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="c4542-151">This is the default used in [Visual Studio](https://www.visualstudio.com/) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="c4542-152">Para usar o IIS como um proxy reverso, chame [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) como parte da compilação do host.</span><span class="sxs-lookup"><span data-stu-id="c4542-152">To use IIS as a reverse proxy, call [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) as part of building the host.</span></span> <span data-ttu-id="c4542-153">`UseIISIntegration` não configura um *servidor* como [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) o faz.</span><span class="sxs-lookup"><span data-stu-id="c4542-153">`UseIISIntegration` doesn't configure a *server*, like [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) does.</span></span> <span data-ttu-id="c4542-154">`UseIISIntegration` configura o caminho base e a porta que o servidor escuta ao usar o [Módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) para criar um proxy reverso entre o Kestrel e o IIS.</span><span class="sxs-lookup"><span data-stu-id="c4542-154">`UseIISIntegration` configures the base path and port the server listens on when using the [ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) to create a reverse proxy between Kestrel and IIS.</span></span> <span data-ttu-id="c4542-155">Para usar o IIS com o ASP.NET Core, tanto `UseKestrel` quanto `UseIISIntegration` precisam ser especificados.</span><span class="sxs-lookup"><span data-stu-id="c4542-155">To use IIS with ASP.NET Core, `UseKestrel` and `UseIISIntegration` must be specified.</span></span> <span data-ttu-id="c4542-156">`UseIISIntegration` é ativado somente quando é executado por trás do IIS ou do IIS Express.</span><span class="sxs-lookup"><span data-stu-id="c4542-156">`UseIISIntegration` only activates when running behind IIS or IIS Express.</span></span> <span data-ttu-id="c4542-157">Para obter mais informações, consulte [Referência de configuração do módulo do ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) e [Introdução ao Módulo do ASP.NET Core](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="c4542-157">For more information, see [Introduction to ASP.NET Core Module](xref:fundamentals/servers/aspnet-core-module) and [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module).</span></span>

<span data-ttu-id="c4542-158">Uma implementação mínima que configura um host (e um aplicativo ASP.NET Core) inclui a especificação de um servidor e a configuração do pipeline de solicitações do aplicativo:</span><span class="sxs-lookup"><span data-stu-id="c4542-158">A minimal implementation that configures a host (and an ASP.NET Core app) includes specifying a server and configuration of the app's request pipeline:</span></span>

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

---

<span data-ttu-id="c4542-159">Ao configurar um host, os métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) e [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) podem ser fornecidos.</span><span class="sxs-lookup"><span data-stu-id="c4542-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) methods can be provided.</span></span> <span data-ttu-id="c4542-160">Se uma classe `Startup` for especificada, ela deverá definir um método `Configure`.</span><span class="sxs-lookup"><span data-stu-id="c4542-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="c4542-161">Para obter mais informações, consulte [Inicialização do aplicativo no ASP.NET Core](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c4542-161">For more information, see [Application Startup in ASP.NET Core](xref:fundamentals/startup).</span></span> <span data-ttu-id="c4542-162">Diversas chamadas para `ConfigureServices` são acrescentadas umas às outras.</span><span class="sxs-lookup"><span data-stu-id="c4542-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="c4542-163">Diversas chamadas para `Configure` ou `UseStartup` no `WebHostBuilder` substituem configurações anteriores.</span><span class="sxs-lookup"><span data-stu-id="c4542-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c4542-164">Valores de configuração do host</span><span class="sxs-lookup"><span data-stu-id="c4542-164">Host configuration values</span></span>

<span data-ttu-id="c4542-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) conta com as seguintes abordagens para definir os valores de configuração do host:</span><span class="sxs-lookup"><span data-stu-id="c4542-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="c4542-166">Configuração do construtor do host, que inclui variáveis de ambiente com o formato `ASPNETCORE_{configurationKey}`.</span><span class="sxs-lookup"><span data-stu-id="c4542-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="c4542-167">Por exemplo, `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c4542-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="c4542-168">Métodos explícitos, como [HostingAbstractionsWebHostBuilderExtensions.UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot).</span><span class="sxs-lookup"><span data-stu-id="c4542-168">Explicit methods, such as [HostingAbstractionsWebHostBuilderExtensions.UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot).</span></span>
* <span data-ttu-id="c4542-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) e a chave associada.</span><span class="sxs-lookup"><span data-stu-id="c4542-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="c4542-170">Ao definir um valor com `UseSetting`, o valor é definido como uma cadeia de caracteres, independentemente do tipo.</span><span class="sxs-lookup"><span data-stu-id="c4542-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="c4542-171">O host usa a opção que define um valor por último.</span><span class="sxs-lookup"><span data-stu-id="c4542-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="c4542-172">Para obter mais informações, veja [Substituir configuração](#override-configuration) na próxima seção.</span><span class="sxs-lookup"><span data-stu-id="c4542-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="capture-startup-errors"></a><span data-ttu-id="c4542-173">Capturar erros de inicialização</span><span class="sxs-lookup"><span data-stu-id="c4542-173">Capture Startup Errors</span></span>

<span data-ttu-id="c4542-174">Esta configuração controla a captura de erros de inicialização.</span><span class="sxs-lookup"><span data-stu-id="c4542-174">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="c4542-175">**Chave**: captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="c4542-175">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="c4542-176">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="c4542-176">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="c4542-177">**Padrão**: o padrão é `false`, a menos que o aplicativo seja executado com o Kestrel por trás do IIS, em que o padrão é `true`.</span><span class="sxs-lookup"><span data-stu-id="c4542-177">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="c4542-178">**Definido usando**: `CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="c4542-178">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="c4542-179">**Variável de ambiente**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="c4542-179">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="c4542-180">Quando `false`, erros durante a inicialização resultam no encerramento do host.</span><span class="sxs-lookup"><span data-stu-id="c4542-180">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="c4542-181">Quando `true`, o host captura exceções durante a inicialização e tenta iniciar o servidor.</span><span class="sxs-lookup"><span data-stu-id="c4542-181">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-182">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-182">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-183">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-183">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
```

---

### <a name="content-root"></a><span data-ttu-id="c4542-184">Raiz do conteúdo</span><span class="sxs-lookup"><span data-stu-id="c4542-184">Content Root</span></span>

<span data-ttu-id="c4542-185">Essa configuração determina onde o ASP.NET Core começa a procurar por arquivos de conteúdo, como exibições do MVC.</span><span class="sxs-lookup"><span data-stu-id="c4542-185">This setting determines where ASP.NET Core begins searching for content files, such as MVC views.</span></span> 

<span data-ttu-id="c4542-186">**Chave**: contentRoot</span><span class="sxs-lookup"><span data-stu-id="c4542-186">**Key**: contentRoot</span></span>  
<span data-ttu-id="c4542-187">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-187">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-188">**Padrão**: o padrão é a pasta em que o assembly do aplicativo reside.</span><span class="sxs-lookup"><span data-stu-id="c4542-188">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="c4542-189">**Definido usando**: `UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="c4542-189">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="c4542-190">**Variável de ambiente**: `ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="c4542-190">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="c4542-191">A raiz do conteúdo também é usada como o caminho base para a [Configuração da raiz da Web](#web-root).</span><span class="sxs-lookup"><span data-stu-id="c4542-191">The content root is also used as the base path for the [Web Root setting](#web-root).</span></span> <span data-ttu-id="c4542-192">Se o caminho não existir, o host não será iniciado.</span><span class="sxs-lookup"><span data-stu-id="c4542-192">If the path doesn't exist, the host fails to start.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-193">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-193">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-194">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-194">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\<content-root>")
```

---

### <a name="detailed-errors"></a><span data-ttu-id="c4542-195">Erros detalhados</span><span class="sxs-lookup"><span data-stu-id="c4542-195">Detailed Errors</span></span>

<span data-ttu-id="c4542-196">Determina se erros detalhados devem ser capturados.</span><span class="sxs-lookup"><span data-stu-id="c4542-196">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="c4542-197">**Chave**: detailedErrors</span><span class="sxs-lookup"><span data-stu-id="c4542-197">**Key**: detailedErrors</span></span>  
<span data-ttu-id="c4542-198">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="c4542-198">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="c4542-199">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="c4542-199">**Default**: false</span></span>  
<span data-ttu-id="c4542-200">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="c4542-200">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="c4542-201">**Variável de ambiente**: `ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="c4542-201">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="c4542-202">Quando habilitado (ou quando o <a href="#environment">Ambiente</a> é definido como `Development`), o aplicativo captura exceções detalhadas.</span><span class="sxs-lookup"><span data-stu-id="c4542-202">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-203">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-203">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-204">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-204">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

---

### <a name="environment"></a><span data-ttu-id="c4542-205">Ambiente</span><span class="sxs-lookup"><span data-stu-id="c4542-205">Environment</span></span>

<span data-ttu-id="c4542-206">Define o ambiente do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-206">Sets the app's environment.</span></span>

<span data-ttu-id="c4542-207">**Chave**: ambiente</span><span class="sxs-lookup"><span data-stu-id="c4542-207">**Key**: environment</span></span>  
<span data-ttu-id="c4542-208">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-208">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-209">**Padrão**: Production</span><span class="sxs-lookup"><span data-stu-id="c4542-209">**Default**: Production</span></span>  
<span data-ttu-id="c4542-210">**Definido usando**: `UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="c4542-210">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="c4542-211">**Variável de ambiente**: `ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="c4542-211">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="c4542-212">O ambiente pode ser definido como qualquer valor.</span><span class="sxs-lookup"><span data-stu-id="c4542-212">The environment can be set to any value.</span></span> <span data-ttu-id="c4542-213">Os valores definidos pela estrutura incluem `Development`, `Staging` e `Production`.</span><span class="sxs-lookup"><span data-stu-id="c4542-213">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="c4542-214">Os valores não diferenciam maiúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="c4542-214">Values aren't case sensitive.</span></span> <span data-ttu-id="c4542-215">Por padrão, o *Ambiente* é lido da variável de ambiente `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="c4542-215">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="c4542-216">Ao usar o [Visual Studio](https://www.visualstudio.com/), variáveis de ambiente podem ser definidas no arquivo *launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="c4542-216">When using [Visual Studio](https://www.visualstudio.com/), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="c4542-217">Para obter mais informações, veja [Usar vários ambientes](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="c4542-217">For more information, see [Use multiple environments](xref:fundamentals/environments).</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-218">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-218">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-219">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-219">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseEnvironment(EnvironmentName.Development)
```

---

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="c4542-220">Hospedando assemblies de inicialização</span><span class="sxs-lookup"><span data-stu-id="c4542-220">Hosting Startup Assemblies</span></span>

<span data-ttu-id="c4542-221">Define os assemblies de inicialização de hospedagem do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-221">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="c4542-222">**Chave**: hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="c4542-222">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="c4542-223">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-223">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-224">**Padrão**: cadeia de caracteres vazia</span><span class="sxs-lookup"><span data-stu-id="c4542-224">**Default**: Empty string</span></span>  
<span data-ttu-id="c4542-225">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="c4542-225">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="c4542-226">**Variável de ambiente**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="c4542-226">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="c4542-227">Uma cadeia de caracteres delimitada por ponto e vírgula de assemblies de inicialização de hospedagem para carregamento na inicialização.</span><span class="sxs-lookup"><span data-stu-id="c4542-227">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span> <span data-ttu-id="c4542-228">Este recurso é novo no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c4542-228">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="c4542-229">Embora o valor padrão da configuração seja uma cadeia de caracteres vazia, os assemblies de inicialização de hospedagem sempre incluem o assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-229">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="c4542-230">Quando assemblies de inicialização de hospedagem são fornecidos, eles são adicionados ao assembly do aplicativo para carregamento quando o aplicativo compilar seus serviços comuns durante a inicialização.</span><span class="sxs-lookup"><span data-stu-id="c4542-230">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-231">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-231">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-232">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-232">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-233">Este recurso não está disponível no ASP.NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="c4542-233">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prefer-hosting-urls"></a><span data-ttu-id="c4542-234">Preferir URLs de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c4542-234">Prefer Hosting URLs</span></span>

<span data-ttu-id="c4542-235">Indica se o host deve escutar as URLs configuradas com o `WebHostBuilder` em vez daquelas configuradas com a implementação `IServer`.</span><span class="sxs-lookup"><span data-stu-id="c4542-235">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="c4542-236">**Chave**: preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="c4542-236">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="c4542-237">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="c4542-237">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="c4542-238">**Padrão**: true</span><span class="sxs-lookup"><span data-stu-id="c4542-238">**Default**: true</span></span>  
<span data-ttu-id="c4542-239">**Definido usando**: `PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="c4542-239">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="c4542-240">**Variável de ambiente**: `ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="c4542-240">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

<span data-ttu-id="c4542-241">Este recurso é novo no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c4542-241">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-242">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-242">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-243">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-243">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-244">Este recurso não está disponível no ASP.NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="c4542-244">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="prevent-hosting-startup"></a><span data-ttu-id="c4542-245">Impedir inicialização de hospedagem</span><span class="sxs-lookup"><span data-stu-id="c4542-245">Prevent Hosting Startup</span></span>

<span data-ttu-id="c4542-246">Impede o carregamento automático de assemblies de inicialização de hospedagem, incluindo assemblies de inicialização de hospedagem configurados pelo assembly do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-246">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="c4542-247">Confira [Aprimorar um aplicativo em um assembly externo com IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) para obter mais informações.</span><span class="sxs-lookup"><span data-stu-id="c4542-247">See [Enhance an app from an external assembly with IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration) for more information.</span></span>

<span data-ttu-id="c4542-248">**Chave**: preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="c4542-248">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="c4542-249">**Tipo**: *bool* (`true` ou `1`)</span><span class="sxs-lookup"><span data-stu-id="c4542-249">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="c4542-250">**Padrão**: falso</span><span class="sxs-lookup"><span data-stu-id="c4542-250">**Default**: false</span></span>  
<span data-ttu-id="c4542-251">**Definido usando**: `UseSetting`</span><span class="sxs-lookup"><span data-stu-id="c4542-251">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="c4542-252">**Variável de ambiente**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="c4542-252">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

<span data-ttu-id="c4542-253">Este recurso é novo no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c4542-253">This feature is new in ASP.NET Core 2.0.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-254">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-254">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-255">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-255">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-256">Este recurso não está disponível no ASP.NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="c4542-256">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="server-urls"></a><span data-ttu-id="c4542-257">URLs de servidor</span><span class="sxs-lookup"><span data-stu-id="c4542-257">Server URLs</span></span>

<span data-ttu-id="c4542-258">Indica os endereços IP ou endereços de host com portas e protocolos que o servidor deve escutar para solicitações.</span><span class="sxs-lookup"><span data-stu-id="c4542-258">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="c4542-259">**Chave**: urls</span><span class="sxs-lookup"><span data-stu-id="c4542-259">**Key**: urls</span></span>  
<span data-ttu-id="c4542-260">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-260">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-261">**Padrão**: http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="c4542-261">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="c4542-262">**Definido usando**: `UseUrls`</span><span class="sxs-lookup"><span data-stu-id="c4542-262">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="c4542-263">**Variável de ambiente**: `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="c4542-263">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="c4542-264">Defina como uma lista separada por ponto e vírgula (;) de prefixos de URL aos quais o servidor deve responder.</span><span class="sxs-lookup"><span data-stu-id="c4542-264">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="c4542-265">Por exemplo, `http://localhost:123`.</span><span class="sxs-lookup"><span data-stu-id="c4542-265">For example, `http://localhost:123`.</span></span> <span data-ttu-id="c4542-266">Use "\*" para indicar que o servidor deve escutar solicitações em qualquer endereço IP ou nome do host usando a porta e o protocolo especificados (por exemplo, `http://*:5000`).</span><span class="sxs-lookup"><span data-stu-id="c4542-266">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="c4542-267">O protocolo (`http://` ou `https://`) deve ser incluído com cada URL.</span><span class="sxs-lookup"><span data-stu-id="c4542-267">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="c4542-268">Os formatos compatíveis variam dependendo dos servidores.</span><span class="sxs-lookup"><span data-stu-id="c4542-268">Supported formats vary between servers.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-269">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-269">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="c4542-270">O Kestrel tem sua própria API de configuração de ponto de extremidade.</span><span class="sxs-lookup"><span data-stu-id="c4542-270">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="c4542-271">Para obter mais informações, consulte [Implementação do servidor Web Kestrel no ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="c4542-271">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel?tabs=aspnetcore2x#endpoint-configuration).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-272">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-272">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

---

### <a name="shutdown-timeout"></a><span data-ttu-id="c4542-273">Tempo limite de desligamento</span><span class="sxs-lookup"><span data-stu-id="c4542-273">Shutdown Timeout</span></span>

<span data-ttu-id="c4542-274">Especifica o tempo de espera para o desligamento do host da Web.</span><span class="sxs-lookup"><span data-stu-id="c4542-274">Specifies the amount of time to wait for the web host to shut down.</span></span>

<span data-ttu-id="c4542-275">**Chave**: shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="c4542-275">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="c4542-276">**Tipo**: *int*</span><span class="sxs-lookup"><span data-stu-id="c4542-276">**Type**: *int*</span></span>  
<span data-ttu-id="c4542-277">**Padrão**: 5</span><span class="sxs-lookup"><span data-stu-id="c4542-277">**Default**: 5</span></span>  
<span data-ttu-id="c4542-278">**Definido usando**: `UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="c4542-278">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="c4542-279">**Variável de ambiente**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="c4542-279">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="c4542-280">Embora a chave aceite um *int* com `UseSetting` (por exemplo, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), o método de extensão [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) usa um [TimeSpan](/dotnet/api/system.timespan).</span><span class="sxs-lookup"><span data-stu-id="c4542-280">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span> <span data-ttu-id="c4542-281">Este recurso é novo no ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="c4542-281">This feature is new in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="c4542-282">Durante o período de tempo limite, a hospedagem:</span><span class="sxs-lookup"><span data-stu-id="c4542-282">During the timeout period, hosting:</span></span>

* <span data-ttu-id="c4542-283">Dispara [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span><span class="sxs-lookup"><span data-stu-id="c4542-283">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="c4542-284">Tenta parar os serviços hospedados, registrando em log os erros dos serviços que falham ao parar.</span><span class="sxs-lookup"><span data-stu-id="c4542-284">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="c4542-285">Se o período de tempo limite expirar antes que todos os serviços hospedados parem, os serviços ativos restantes serão parados quando o aplicativo for desligado.</span><span class="sxs-lookup"><span data-stu-id="c4542-285">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="c4542-286">Os serviços serão parados mesmo se ainda não tiverem concluído o processamento.</span><span class="sxs-lookup"><span data-stu-id="c4542-286">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="c4542-287">Se os serviços exigirem mais tempo para parar, aumente o tempo limite.</span><span class="sxs-lookup"><span data-stu-id="c4542-287">If services require additional time to stop, increase the timeout.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-288">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-288">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-289">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-289">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-290">Este recurso não está disponível no ASP.NET Core 1. x.</span><span class="sxs-lookup"><span data-stu-id="c4542-290">This feature is unavailable in ASP.NET Core 1.x.</span></span>

---

### <a name="startup-assembly"></a><span data-ttu-id="c4542-291">Assembly de inicialização</span><span class="sxs-lookup"><span data-stu-id="c4542-291">Startup Assembly</span></span>

<span data-ttu-id="c4542-292">Determina o assembly para pesquisar pela classe `Startup`.</span><span class="sxs-lookup"><span data-stu-id="c4542-292">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="c4542-293">**Chave**: startupAssembly</span><span class="sxs-lookup"><span data-stu-id="c4542-293">**Key**: startupAssembly</span></span>  
<span data-ttu-id="c4542-294">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-294">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-295">**Padrão**: o assembly do aplicativo</span><span class="sxs-lookup"><span data-stu-id="c4542-295">**Default**: The app's assembly</span></span>  
<span data-ttu-id="c4542-296">**Definido usando**: `UseStartup`</span><span class="sxs-lookup"><span data-stu-id="c4542-296">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="c4542-297">**Variável de ambiente**: `ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="c4542-297">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="c4542-298">O assembly por nome (`string`) ou por tipo (`TStartup`) pode ser referenciado.</span><span class="sxs-lookup"><span data-stu-id="c4542-298">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="c4542-299">Se vários métodos `UseStartup` forem chamados, o último terá precedência.</span><span class="sxs-lookup"><span data-stu-id="c4542-299">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-300">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-300">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-301">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-301">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
```

---

### <a name="web-root"></a><span data-ttu-id="c4542-302">Raiz da Web</span><span class="sxs-lookup"><span data-stu-id="c4542-302">Web Root</span></span>

<span data-ttu-id="c4542-303">Define o caminho relativo para os ativos estáticos do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-303">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="c4542-304">**Chave**: webroot</span><span class="sxs-lookup"><span data-stu-id="c4542-304">**Key**: webroot</span></span>  
<span data-ttu-id="c4542-305">**Tipo**: *string*</span><span class="sxs-lookup"><span data-stu-id="c4542-305">**Type**: *string*</span></span>  
<span data-ttu-id="c4542-306">**Padrão**: se não for especificado, o padrão será "(Raiz do conteúdo)/wwwroot", se o caminho existir.</span><span class="sxs-lookup"><span data-stu-id="c4542-306">**Default**: If not specified, the default is "(Content Root)/wwwroot", if the path exists.</span></span> <span data-ttu-id="c4542-307">Se o caminho não existir, um provedor de arquivo não operacional será usado.</span><span class="sxs-lookup"><span data-stu-id="c4542-307">If the path doesn't exist, then a no-op file provider is used.</span></span>  
<span data-ttu-id="c4542-308">**Definido usando**: `UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="c4542-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="c4542-309">**Variável de ambiente**: `ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="c4542-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-310">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-310">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-311">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-311">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
```

---

## <a name="override-configuration"></a><span data-ttu-id="c4542-312">Substituir configuração</span><span class="sxs-lookup"><span data-stu-id="c4542-312">Override configuration</span></span>

<span data-ttu-id="c4542-313">Use [Configuração](xref:fundamentals/configuration/index) para configurar o host.</span><span class="sxs-lookup"><span data-stu-id="c4542-313">Use [Configuration](xref:fundamentals/configuration/index) to configure the host.</span></span> <span data-ttu-id="c4542-314">No exemplo a seguir, a configuração do host é especificada, opcionalmente, em um arquivo *hosting.json*.</span><span class="sxs-lookup"><span data-stu-id="c4542-314">In the following example, host configuration is optionally specified in a *hosting.json* file.</span></span> <span data-ttu-id="c4542-315">Qualquer configuração carregada do arquivo *hosting.json* pode ser substituída por argumentos de linha de comando.</span><span class="sxs-lookup"><span data-stu-id="c4542-315">Any configuration loaded from the *hosting.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="c4542-316">A configuração interna (no `config`) é usada para configurar o host com `UseConfiguration`.</span><span class="sxs-lookup"><span data-stu-id="c4542-316">The built configuration (in `config`) is used to configure the host with `UseConfiguration`.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-317">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-317">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c4542-318">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="c4542-318">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="c4542-319">Substituição da configuração fornecida por `UseUrls` pela configuração de *hosting.json* primeiro e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="c4542-319">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-320">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-320">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-321">*hosting.json*:</span><span class="sxs-lookup"><span data-stu-id="c4542-321">*hosting.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

<span data-ttu-id="c4542-322">Substituição da configuração fornecida por `UseUrls` pela configuração de *hosting.json* primeiro e pela configuração de argumento da linha de comando depois:</span><span class="sxs-lookup"><span data-stu-id="c4542-322">Overriding the configuration provided by `UseUrls` with *hosting.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hosting.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

---

> [!NOTE]
> <span data-ttu-id="c4542-323">Atualmente, o método de extensão [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) não é capaz de analisar uma seção de configuração retornada por `GetSection` (por exemplo, `.UseConfiguration(Configuration.GetSection("section"))`).</span><span class="sxs-lookup"><span data-stu-id="c4542-323">The [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) extension method isn't currently capable of parsing a configuration section returned by `GetSection` (for example, `.UseConfiguration(Configuration.GetSection("section"))`.</span></span> <span data-ttu-id="c4542-324">O método `GetSection` filtra as chaves de configuração da seção solicitada, mas deixa o nome da seção nas chaves (por exemplo, `section:urls`, `section:environment`).</span><span class="sxs-lookup"><span data-stu-id="c4542-324">The `GetSection` method filters the configuration keys to the section requested but leaves the section name on the keys (for example, `section:urls`, `section:environment`).</span></span> <span data-ttu-id="c4542-325">O método `UseConfiguration` espera que as chaves correspondam às chaves `WebHostBuilder` (por exemplo, `urls`, `environment`).</span><span class="sxs-lookup"><span data-stu-id="c4542-325">The `UseConfiguration` method expects the keys to match the `WebHostBuilder` keys (for example, `urls`, `environment`).</span></span> <span data-ttu-id="c4542-326">A presença do nome da seção nas chaves impede que os valores da seção configurem o host.</span><span class="sxs-lookup"><span data-stu-id="c4542-326">The presence of the section name on the keys prevents the section's values from configuring the host.</span></span> <span data-ttu-id="c4542-327">Esse problema será corrigido em uma próxima versão.</span><span class="sxs-lookup"><span data-stu-id="c4542-327">This issue will be addressed in an upcoming release.</span></span> <span data-ttu-id="c4542-328">Para obter mais informações e soluções alternativas, consulte [Passar a seção de configuração para WebHostBuilder.UseConfiguration usa chaves completas](https://github.com/aspnet/Hosting/issues/839).</span><span class="sxs-lookup"><span data-stu-id="c4542-328">For more information and workarounds, see [Passing configuration section into WebHostBuilder.UseConfiguration uses full keys](https://github.com/aspnet/Hosting/issues/839).</span></span>

<span data-ttu-id="c4542-329">Para especificar o host executado em uma URL específica, o valor desejado pode ser passado em um prompt de comando ao executar [dotnet run](/dotnet/core/tools/dotnet-run).</span><span class="sxs-lookup"><span data-stu-id="c4542-329">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="c4542-330">O argumento de linha de comando substitui o valor `urls` do arquivo *hosting.json* e o servidor escuta na porta 8080:</span><span class="sxs-lookup"><span data-stu-id="c4542-330">The command-line argument overrides the `urls` value from the *hosting.json* file, and the server listens on port 8080:</span></span>

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="c4542-331">Gerenciar o host</span><span class="sxs-lookup"><span data-stu-id="c4542-331">Manage the host</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="c4542-332">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="c4542-332">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="c4542-333">**Executar**</span><span class="sxs-lookup"><span data-stu-id="c4542-333">**Run**</span></span>

<span data-ttu-id="c4542-334">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="c4542-334">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="c4542-335">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="c4542-335">**Start**</span></span>

<span data-ttu-id="c4542-336">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="c4542-336">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="c4542-337">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="c4542-337">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="c4542-338">O aplicativo pode inicializar e iniciar um novo host usando os padrões pré-configurados de `CreateDefaultBuilder`, usando um método estático conveniente.</span><span class="sxs-lookup"><span data-stu-id="c4542-338">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="c4542-339">Esses métodos iniciam o servidor sem uma saída do console e com [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) e aguardam uma quebra (Ctrl-C/SIGINT ou SIGTERM):</span><span class="sxs-lookup"><span data-stu-id="c4542-339">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="c4542-340">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="c4542-340">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="c4542-341">Inicie com um `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="c4542-341">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-342">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="c4542-342">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="c4542-343">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="c4542-343">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="c4542-344">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="c4542-344">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="c4542-345">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="c4542-345">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="c4542-346">Inicie com uma URL e `RequestDelegate`:</span><span class="sxs-lookup"><span data-stu-id="c4542-346">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-347">Produz o mesmo resultado que **Start(RequestDelegate app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="c4542-347">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="c4542-348">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="c4542-348">**Start(Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="c4542-349">Use uma instância de `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) para usar o middleware de roteamento:</span><span class="sxs-lookup"><span data-stu-id="c4542-349">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-350">Use as seguintes solicitações de navegador com o exemplo:</span><span class="sxs-lookup"><span data-stu-id="c4542-350">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="c4542-351">Solicitação</span><span class="sxs-lookup"><span data-stu-id="c4542-351">Request</span></span>                                    | <span data-ttu-id="c4542-352">Resposta</span><span class="sxs-lookup"><span data-stu-id="c4542-352">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="c4542-353">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="c4542-353">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="c4542-354">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="c4542-354">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="c4542-355">Gera uma exceção com a cadeia de caracteres "ooops!"</span><span class="sxs-lookup"><span data-stu-id="c4542-355">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="c4542-356">Gera uma exceção com a cadeia de caracteres "Uh oh!"</span><span class="sxs-lookup"><span data-stu-id="c4542-356">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="c4542-357">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="c4542-357">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="c4542-358">Olá, Mundo!</span><span class="sxs-lookup"><span data-stu-id="c4542-358">Hello World!</span></span>                             |

<span data-ttu-id="c4542-359">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="c4542-359">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="c4542-360">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="c4542-360">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="c4542-361">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="c4542-361">**Start(string url, Action&lt;IRouteBuilder&gt; routeBuilder)**</span></span>

<span data-ttu-id="c4542-362">Use uma URL e uma instância de `IRouteBuilder`:</span><span class="sxs-lookup"><span data-stu-id="c4542-362">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-363">Produz o mesmo resultado que **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="c4542-363">Produces the same result as **Start(Action&lt;IRouteBuilder&gt; routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="c4542-364">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="c4542-364">**StartWith(Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="c4542-365">Forneça um delegado para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="c4542-365">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-366">Faça uma solicitação no navegador para `http://localhost:5000` para receber a resposta "Olá, Mundo!"</span><span class="sxs-lookup"><span data-stu-id="c4542-366">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="c4542-367">`WaitForShutdown` bloqueia até que uma quebra (Ctrl-C/SIGINT ou SIGTERM) seja emitida.</span><span class="sxs-lookup"><span data-stu-id="c4542-367">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="c4542-368">O aplicativo exibe a mensagem `Console.WriteLine` e aguarda um pressionamento de tecla para ser encerrado.</span><span class="sxs-lookup"><span data-stu-id="c4542-368">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="c4542-369">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span><span class="sxs-lookup"><span data-stu-id="c4542-369">**StartWith(string url, Action&lt;IApplicationBuilder&gt; app)**</span></span>

<span data-ttu-id="c4542-370">Forneça um delegado e uma URL para configurar um `IApplicationBuilder`:</span><span class="sxs-lookup"><span data-stu-id="c4542-370">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="c4542-371">Produz o mesmo resultado que **StartWith(Action&lt;IApplicationBuilder&gt; app)**, mas o aplicativo responde em `http://localhost:8080`.</span><span class="sxs-lookup"><span data-stu-id="c4542-371">Produces the same result as **StartWith(Action&lt;IApplicationBuilder&gt; app)**, except the app responds on `http://localhost:8080`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="c4542-372">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="c4542-372">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="c4542-373">**Executar**</span><span class="sxs-lookup"><span data-stu-id="c4542-373">**Run**</span></span>

<span data-ttu-id="c4542-374">O método `Run` inicia o aplicativo Web e bloqueia o thread de chamada até que o host seja desligado:</span><span class="sxs-lookup"><span data-stu-id="c4542-374">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="c4542-375">**Iniciar**</span><span class="sxs-lookup"><span data-stu-id="c4542-375">**Start**</span></span>

<span data-ttu-id="c4542-376">Execute o host sem bloqueio, chamando seu método `Start`:</span><span class="sxs-lookup"><span data-stu-id="c4542-376">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="c4542-377">Se uma lista de URLs for passada para o método `Start`, ele escutará nas URLs especificadas:</span><span class="sxs-lookup"><span data-stu-id="c4542-377">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

---

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="c4542-378">Interface IHostingEnvironment</span><span class="sxs-lookup"><span data-stu-id="c4542-378">IHostingEnvironment interface</span></span>

<span data-ttu-id="c4542-379">A [interface IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) fornece informações sobre o ambiente de hospedagem na Web do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-379">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="c4542-380">Use a [injeção de construtor](xref:fundamentals/dependency-injection) para obter o `IHostingEnvironment` para usar suas propriedades e métodos de extensão:</span><span class="sxs-lookup"><span data-stu-id="c4542-380">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="c4542-381">Uma [abordagem baseada em convenção](xref:fundamentals/environments#startup-conventions) pode ser usada para configurar o aplicativo na inicialização com base no ambiente.</span><span class="sxs-lookup"><span data-stu-id="c4542-381">A [convention-based approach](xref:fundamentals/environments#startup-conventions) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="c4542-382">Como alternativa, injete o `IHostingEnvironment` no construtor `Startup` para uso em `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="c4542-382">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="c4542-383">Além do método de extensão `IsDevelopment`, `IHostingEnvironment` oferece os métodos `IsStaging`, `IsProduction` e `IsEnvironment(string environmentName)`.</span><span class="sxs-lookup"><span data-stu-id="c4542-383">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="c4542-384">Confira [Usar vários ambientes](xref:fundamentals/environments) para obter detalhes.</span><span class="sxs-lookup"><span data-stu-id="c4542-384">See [Use multiple environments](xref:fundamentals/environments) for details.</span></span>

<span data-ttu-id="c4542-385">O serviço `IHostingEnvironment` também pode ser injetado diretamente no método `Configure` para configurar o pipeline de processamento:</span><span class="sxs-lookup"><span data-stu-id="c4542-385">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="c4542-386">`IHostingEnvironment` pode ser injetado no método `Invoke` ao criar um [middleware](xref:fundamentals/middleware/index#writing-middleware) personalizado:</span><span class="sxs-lookup"><span data-stu-id="c4542-386">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/index#writing-middleware):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="c4542-387">Interface IApplicationLifetime</span><span class="sxs-lookup"><span data-stu-id="c4542-387">IApplicationLifetime interface</span></span>

<span data-ttu-id="c4542-388">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) permite atividades pós-inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="c4542-388">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="c4542-389">Três propriedades na interface são tokens de cancelamento usados para registrar métodos `Action` que definem eventos de inicialização e desligamento.</span><span class="sxs-lookup"><span data-stu-id="c4542-389">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="c4542-390">Token de cancelamento</span><span class="sxs-lookup"><span data-stu-id="c4542-390">Cancellation Token</span></span>    | <span data-ttu-id="c4542-391">Acionado quando&#8230;</span><span class="sxs-lookup"><span data-stu-id="c4542-391">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="c4542-392">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="c4542-392">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="c4542-393">O host foi iniciado totalmente.</span><span class="sxs-lookup"><span data-stu-id="c4542-393">The host has fully started.</span></span> |
| [<span data-ttu-id="c4542-394">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="c4542-394">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="c4542-395">O host está concluindo um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="c4542-395">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="c4542-396">Todas as solicitações devem ser processadas.</span><span class="sxs-lookup"><span data-stu-id="c4542-396">All requests should be processed.</span></span> <span data-ttu-id="c4542-397">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="c4542-397">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="c4542-398">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="c4542-398">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="c4542-399">O host está executando um desligamento normal.</span><span class="sxs-lookup"><span data-stu-id="c4542-399">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="c4542-400">Solicitações ainda podem estar sendo processadas.</span><span class="sxs-lookup"><span data-stu-id="c4542-400">Requests may still be processing.</span></span> <span data-ttu-id="c4542-401">O desligamento é bloqueado até que esse evento seja concluído.</span><span class="sxs-lookup"><span data-stu-id="c4542-401">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="c4542-402">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) solicita o término do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="c4542-402">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="c4542-403">A classe a seguir usa `StopApplication` para desligar normalmente um aplicativo quando o método `Shutdown` da classe é chamado:</span><span class="sxs-lookup"><span data-stu-id="c4542-403">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="scope-validation"></a><span data-ttu-id="c4542-404">Validação de escopo</span><span class="sxs-lookup"><span data-stu-id="c4542-404">Scope validation</span></span>

<span data-ttu-id="c4542-405">No ASP.NET Core 2.0 ou posterior, [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) define [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) como `true` quando o ambiente do aplicativo é de desenvolvimento.</span><span class="sxs-lookup"><span data-stu-id="c4542-405">In ASP.NET Core 2.0 or later, [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="c4542-406">Quando `ValidateScopes` está definido como `true`, o provedor de serviço padrão executa verificações para saber se:</span><span class="sxs-lookup"><span data-stu-id="c4542-406">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="c4542-407">Os serviços com escopo não são resolvidos direta ou indiretamente pelo provedor de serviço raiz.</span><span class="sxs-lookup"><span data-stu-id="c4542-407">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="c4542-408">Os serviços com escopo não são injetados direta ou indiretamente em singletons.</span><span class="sxs-lookup"><span data-stu-id="c4542-408">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="c4542-409">O provedor de serviços raiz é criado quando [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) é chamado.</span><span class="sxs-lookup"><span data-stu-id="c4542-409">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="c4542-410">O tempo de vida do provedor de serviço raiz corresponde ao tempo de vida do aplicativo/servidor quando o provedor começa com o aplicativo e é descartado quando o aplicativo é desligado.</span><span class="sxs-lookup"><span data-stu-id="c4542-410">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="c4542-411">Os serviços com escopo são descartados pelo contêiner que os criou.</span><span class="sxs-lookup"><span data-stu-id="c4542-411">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="c4542-412">Se um serviço com escopo é criado no contêiner raiz, o tempo de vida do serviço é promovido efetivamente para singleton, porque ele só é descartado pelo contêiner raiz quando o aplicativo/servidor é desligado.</span><span class="sxs-lookup"><span data-stu-id="c4542-412">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="c4542-413">A validação dos escopos de serviço detecta essas situações quando `BuildServiceProvider` é chamado.</span><span class="sxs-lookup"><span data-stu-id="c4542-413">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="c4542-414">Para que os escopos sempre sejam validados, incluindo no ambiente de produção, configure [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) com [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) no construtor do host:</span><span class="sxs-lookup"><span data-stu-id="c4542-414">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="troubleshooting-systemargumentexception"></a><span data-ttu-id="c4542-415">Solução de problemas de System.ArgumentException</span><span class="sxs-lookup"><span data-stu-id="c4542-415">Troubleshooting System.ArgumentException</span></span>

<span data-ttu-id="c4542-416">**Aplica-se somente ao ASP.NET Core 2.0**</span><span class="sxs-lookup"><span data-stu-id="c4542-416">**Applies to ASP.NET Core 2.0 Only**</span></span>

<span data-ttu-id="c4542-417">Um host pode ser compilado injetando `IStartup` diretamente no contêiner de injeção de dependência em vez de chamar `UseStartup` ou `Configure`:</span><span class="sxs-lookup"><span data-stu-id="c4542-417">A host may be built by injecting `IStartup` directly into the dependency injection container rather than calling `UseStartup` or `Configure`:</span></span>

```csharp
services.AddSingleton<IStartup, Startup>();
```

<span data-ttu-id="c4542-418">Se o host for compilado dessa maneira, o seguinte erro poderá ocorrer:</span><span class="sxs-lookup"><span data-stu-id="c4542-418">If the host is built this way, the following error may occur:</span></span>

```
Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided.
```

<span data-ttu-id="c4542-419">Isso ocorre porque o [applicationName(ApplicationKey)](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (do assembly atual) é necessário para verificar se há `HostingStartupAttributes`.</span><span class="sxs-lookup"><span data-stu-id="c4542-419">This occurs because the [applicationName(ApplicationKey)](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults#Microsoft_AspNetCore_Hosting_WebHostDefaults_ApplicationKey) (the current assembly) is required to scan for `HostingStartupAttributes`.</span></span> <span data-ttu-id="c4542-420">Se o aplicativo injetar manualmente `IStartup` no contêiner de injeção de dependência, adicione a seguinte chamada para `WebHostBuilder` com o nome do assembly especificado:</span><span class="sxs-lookup"><span data-stu-id="c4542-420">If the app manually injects `IStartup` into the dependency injection container, add the following call to `WebHostBuilder` with the assembly name specified:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "<Assembly Name>")
    ...
```

<span data-ttu-id="c4542-421">Como alternativa, adicione um `Configure` fictício ao `WebHostBuilder`, que define o `applicationName`(`ApplicationKey`) automaticamente:</span><span class="sxs-lookup"><span data-stu-id="c4542-421">Alternatively, add a dummy `Configure` to the `WebHostBuilder`, which sets the `applicationName`(`ApplicationKey`) automatically:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
    ...
```

<span data-ttu-id="c4542-422">**Observação**: isso só é necessário com a versão do ASP.NET Core 2.0 e somente quando o aplicativo não chama `UseStartup` ou `Configure`.</span><span class="sxs-lookup"><span data-stu-id="c4542-422">**NOTE**: This is only required with the ASP.NET Core 2.0 release and only when the app doesn't call `UseStartup` or `Configure`.</span></span>

<span data-ttu-id="c4542-423">Para obter mais informações, consulte [Comunicado: Microsoft.Extensions.PlatformAbstractions foi removido (comentário)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) e o [Exemplo de StartupInjection](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span><span class="sxs-lookup"><span data-stu-id="c4542-423">For more information, see [Announcements: Microsoft.Extensions.PlatformAbstractions has been removed (comment)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) and the [StartupInjection sample](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c4542-424">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="c4542-424">Additional resources</span></span>

* [<span data-ttu-id="c4542-425">Hospedar no Windows com o IIS</span><span class="sxs-lookup"><span data-stu-id="c4542-425">Host on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="c4542-426">Hospedar em Linux com o Nginx</span><span class="sxs-lookup"><span data-stu-id="c4542-426">Host on Linux with Nginx</span></span>](xref:host-and-deploy/linux-nginx)
* [<span data-ttu-id="c4542-427">Hospedar em Linux com o Apache</span><span class="sxs-lookup"><span data-stu-id="c4542-427">Host on Linux with Apache</span></span>](xref:host-and-deploy/linux-apache)
* [<span data-ttu-id="c4542-428">Hospedar em um serviço Windows</span><span class="sxs-lookup"><span data-stu-id="c4542-428">Host in a Windows Service</span></span>](xref:host-and-deploy/windows-service)