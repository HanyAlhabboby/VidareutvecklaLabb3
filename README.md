# Vidareutveckla Designmönster och arkitektur

### Kopiera koden under till ditt projekt
    
        
        ```csharp
        using System;
        using System.Collections.Generic;
        using System.Linq;
        
        namespace VarmDrinkStation
        {
            // Definierar ett interface för varma drycker
            public interface IWarmDrink
            {
                void Consume(); // Metod för att konsumera drycken
            }
        
            // Implementerar en specifik varm dryck, i detta fall vatten
            internal class Water : IWarmDrink
            {
                public void Consume()
                {
                    Console.WriteLine("Warm water is served."); // Utskrift vid konsumtion av vatten
                }
            }
        
            // Definierar ett interface för fabriker som kan skapa varma drycker
            public interface IWarmDrinkFactory
            {
                IWarmDrink Prepare(int total); // Metod för att förbereda drycken med en specifik mängd
            }
        
            // Implementerar en specifik fabrik som förbereder varmt vatten
            internal class HotWaterFactory : IWarmDrinkFactory
            {
                public IWarmDrink Prepare(int total)
                {
                    Console.WriteLine($"Pour {total} ml hot water in your cup"); // Utskrift av mängden vatten som hälls upp
                    return new Water(); // Returnerar en ny instans av Water
                }
            }
        
            // Maskin som hanterar skapandet av varma drycker
            public class WarmDrinkMachine
            {
                private readonly List<Tuple<string, IWarmDrinkFactory>> namedFactories; // Lista över fabriker med deras namn
        
                public WarmDrinkMachine()
                {
                    namedFactories = new List<Tuple<string, IWarmDrinkFactory>>(); // Initierar listan över fabriker
        
                    // Registrerar fabriker explicit
                    RegisterFactory<HotWaterFactory>("Hot Water"); // Registrerar fabriken för varmt vatten
                }
        
                // Metod för att registrera en fabrik
                private void RegisterFactory<T>(string drinkName) where T : IWarmDrinkFactory, new()
                {
                    namedFactories.Add(Tuple.Create(drinkName, (IWarmDrinkFactory)Activator.CreateInstance(typeof(T)))); // Lägger till fabriken i listan
                }
        
                // Metod för att skapa en varm dryck
                public IWarmDrink MakeDrink()
                {
                    Console.WriteLine("This is what we serve today:");
                    for (var index = 0; index < namedFactories.Count; index++)
                    {
                        var tuple = namedFactories[index];
                        Console.WriteLine($"{index}: {tuple.Item1}"); // Skriver ut tillgängliga drycker
                    }
                    Console.WriteLine("Select a number to continue:");
                    while (true)
                    {
                        if (int.TryParse(Console.ReadLine(), out int i) && i >= 0 && i < namedFactories.Count) // Läser och validerar användarens val
                        {
                            Console.Write("How much: ");
                            if (int.TryParse(Console.ReadLine(), out int total) && total > 0) // Läser och validerar mängden
                            {
                                return namedFactories[i].Item2.Prepare(total); // Förbereder och returnerar drycken
                            }
                        }
                        Console.WriteLine("Something went wrong with your input, try again."); // Meddelande vid felaktig inmatning
                    }
                }
            }
        
            class Program
            {
                static void Main(string[] args)
                {
                    var machine = new WarmDrinkMachine(); // Skapar en instans av WarmDrinkMachine
                    IWarmDrink drink = machine.MakeDrink(); // Skapar en dryck
                    drink.Consume(); // Konsumerar drycken
                }
            }
        }
        
        ```
        
###  Testa först att du kan köra applikationen i befintligt skick
###  Bygg sedan till funktioner för att tillaga/bereda kaffe, cappuccino och choklad.
###  Du ska fortsätta använda samma design mönster som redan finns i applikationen och utöka det utan att lägga till fler design mönster eller sluta använda dem
