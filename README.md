# ApiConsumer_Cache_Redis
![Design sem nome (6)](https://github.com/leandro-guimaraes/MassTransitAllog/assets/85081592/ba9f0f79-3f64-4fb5-bc11-fe6b21d1dc97)

Esse é um projeto de um consumer (consumidor) que utiliza o MassTransit para receber mensagens enviadas pelo producer (produtor) que esta no outro repositório deste github ApiProducer_Cache_Redis.
O MassTransit é uma biblioteca que fornece recursos para implementação de mensageria em aplicações distribuídas.

## Configuração do Consumer

O consumer é uma parte do sistema que recebe e processa mensagens enviadas por outras partes do sistema, geralmente conhecidas como producers. Neste projeto, o consumer está configurado para receber mensagens do RabbitMQ, um sistema de mensageria.

A configuração do consumer é feita através do código C# e utiliza a biblioteca MassTransit. Abaixo estão os principais trechos do código de configuração:

```csharp
using MassTransit;
using Microsoft.Extensions.Hosting;
using System.Reflection;
using System.Security.Authentication;

var builder = Host.CreateDefaultBuilder(args);

builder.ConfigureServices((hostContext, services) =>
{
    services.AddMassTransit(busConfigurator =>
    {
        var entryAssembly = Assembly.GetExecutingAssembly();
        busConfigurator.AddConsumers(entryAssembly);
        busConfigurator.UsingRabbitMq((context, busFactoryConfigurator) =>
        {
            busFactoryConfigurator.Host("jackal-01.rmq.cloudamqp.com", 5671, "orxcfbhg", h =>
            {
                h.Username("orxcfbhg");
                h.Password("Sn0ws-oL3UwS2hW76Bo6Bby-Yhl2MDKD");

                h.UseSsl(s =>
                {
                    s.Protocol = SslProtocols.Tls12;
                });
            });
            busFactoryConfigurator.ConfigureEndpoints(context);
        });
    });
});

var app = builder.Build();

app.Run();
```

## Principais Dependências Utilizadas

- MassTransit 8.0.16: Biblioteca para implementação de mensageria em aplicações distribuídas.
- MassTransit.RabbitMQ 8.0.16: Implementação do MassTransit para uso com o RabbitMQ, um sistema de mensageria.
- Microsoft.Extensions.Hosting 7.0.1: Biblioteca utilizada para criar e configurar o host da aplicação.

## Funcionamento do Consumer

O consumer é configurado para utilizar o RabbitMQ como serviço de mensageria. O código estabelece a conexão com o servidor do RabbitMQ utilizando as credenciais de usuário "orxcfbhg" e senha "Sn0ws-oL3UwS2hW76Bo6Bby-Yhl2MDKD" em uma conexão SSL/TLS segura. Ele também define o protocolo de segurança como Tls12.

A configuração utiliza a reflexão para identificar e adicionar os consumers disponíveis no assembly atual, permitindo que as mensagens enviadas pelo producer sejam corretamente tratadas pelos consumers adequados.

Após a configuração, o consumer utiliza o método `app.Run()` para iniciar o processamento de mensagens do RabbitMQ.

## Executando o Consumer

Para executar o consumer, é necessário configurar corretamente o ambiente com todas as dependências. Recomenda-se utilizar o Visual Studio ou o Visual Studio Code com a extensão do .NET Core.

Ao executar o consumer, ele ficará aguardando a chegada de mensagens enviadas pelo producer, que deve estar configurado para enviar mensagens para a mesma fila (endpoint) do RabbitMQ configurada aqui.

Esse projeto de consumer, em conjunto com o projeto de producer que eu forneci anteriormente, permitirá a implementação de uma comunicação assíncrona entre diferentes partes do sistema, o que é especialmente útil para aplicações distribuídas e escaláveis.
