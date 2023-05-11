# Webservice Client
1. Neues **Console** Projekt anlegen
2. Add ConnectedService, WCF Web Service
3. Update Program.cs

	   static async Task Main(string[] args)
       {
       Console.WriteLine("Hello, World!");
       ServiceReference1.ReverseServiceClient client = new ServiceReference1.ReverseServiceClient();

       string value = "Hello World";
       string rev = await client.ReverseAsync(value);

       Console.WriteLine($@"'{value}' written in reverse is {rev}")
       Console.ReadLine();
       }
# GrpcServer

 1. Neues **ASP NET Core gRPC Service** Projekt anlegen
 2. Add, new Item (Protobuf)

        syntax = "proto3";
        option csharp_namespace = "CalcService.Protos";
        
        service Calculator {
          rpc Div (Arguments) returns (Result);
        }
        
        message Arguments {
          double arg1 = 1;
          double arg2 = 2;
        }
        
        message Result {
          double result = 1;
        }
 3. Verweis in Projektdatei setzen
 4. Add, new File (Class), CalculatorService

        using CalcService.Protos;
        public class CalculatorService : Calculator.CalculatorBase
        {
            public override Task<Result> Div(Arguments request, ServerCallContext context)
            {
                var result = request.Arg1 / request.Arg2;

                Result res = new Result()
                {
                    Result_ = result
                };

                return Task.FromResult(res);
            }
        }
 5. Add Service in Programm.cs



# GrpcClient

 1. Add new Project, Console App, CalcClient 
 2. Protobuf kopieren, namespace anpassen, Projektdatei (Server zu Client)
 3. Nugget-Packages: **Google.Protobuf, Grpc.Net.Client, Grpc.Tools**
 4. Programm.cs Code ändern
 5. localhost-Port anpassen

	    Console.WriteLine("Hello, World!");
        var httpHandler = new HttpClientHandler();
        // Return `true` to allow certificates that are untrusted/invalid
        httpHandler.ServerCertificateCustomValidationCallback = HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

        using var channel = GrpcChannel.ForAddress("https://localhost:7111", new GrpcChannelOptions { HttpHandler = httpHandler }); 

        var client = new Calculator.CalculatorClient(channel);

        var arguments = new Arguments
        {
            Arg1 = 8,
            Arg2 = 2
        };

        var result = client.Div(arguments);

        Console.WriteLine($@"{arguments.Arg1} / {arguments.Arg2} = {result.Result_}");

        Console.ReadLine();

6. Testen
