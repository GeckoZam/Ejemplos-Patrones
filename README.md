# Ejemplos Patrones
## Singleton
### Escenario del mundo real
Imagina que tienes un administrador de impresoras en una oficina. Puede haber varias personas que utilicen la impresora, pero la oficina solo tiene un administrador de impresoras que se encarga de todos los trabajos de impresión. El patrón Singleton garantiza que solo exista una instancia del administrador de impresoras, independientemente de cuántas veces la gente intente "crearla" o acceder a ella.

https://dotnetfiddle.net/O1f5KC
```c#
using System;

public class PrinterManager
{
    // Private static variable to hold the single instance of the class
    private static PrinterManager _instance;

    // Private constructor to prevent instantiation from outside
    private PrinterManager()
    {
        Console.WriteLine("Creating the PrinterManager instance.");
    }

    // Public static method to provide global access point to the single instance
    public static PrinterManager GetInstance()
    {
        if (_instance == null)
        {
            _instance = new PrinterManager();
        }
        return _instance;
    }

    // Method to simulate handling a print job
    public void HandlePrintJob(string job)
    {
        Console.WriteLine($"Handling print job: {job}");
    }
}

public class Program
{
    public static void Main(string[] args)
    {
        // Simulating different employees trying to access the printer manager
        PrinterManager manager1 = PrinterManager.GetInstance();
        PrinterManager manager2 = PrinterManager.GetInstance();

        // Both manager1 and manager2 should refer to the same instance
       	Console.WriteLine($"Manager1 is manager2: {manager1 == manager2}");

        // Handling print jobs
        manager1.HandlePrintJob("Employee1's Document");
        manager2.HandlePrintJob("Employee2's Document");
    }
}
```
## Salida
```python
Creating the PrinterManager instance.
Manager1 is manager2: True
Handling print job: Employee1's Document
Handling print job: Employee2's Document
```
### Explicación
- Constructor privado: el constructor está marcado como privado para que ninguna otra parte del código pueda crear una instancia de la clase directamente.
- Método estático GetInstance(): este método es el punto de acceso global para PrinterManager. Comprueba si la instancia ya existe; si no, la crea y devuelve la instancia única.
- Variable de instancia: la variable _instance contiene la instancia única de PrinterManager y es privada para evitar el acceso externo.

<p>Este código garantiza que, sin importar cuántas veces los empleados (diferentes partes del programa) intenten crear o acceder al PrinterManager, siempre habrá un solo PrinterManager en existencia en cualquier momento. </p>
<p>Este patrón se usa comúnmente en escenarios como la administración de conexiones de bases de datos, sistemas de registro o configuraciones donde solo se debe usar una instancia globalmente en toda una aplicación.</p>

## Factory Method
### Escenario del mundo real
Imagina que trabajas en una empresa de logística que se encarga de las entregas. Según el tipo de transporte (carretera, mar o aire), se utilizan distintos vehículos para entregar las mercancías. El método Factory nos permitirá definir una interfaz común para la creación de vehículos, pero postergará la instanciación a subclases (fábricas para cada tipo de transporte).

La empresa de logística puede entregar mercancías mediante:
- Camión (carretera)
- Barco (mar)
- Avión (aire)

El patrón de método de fábrica proporcionará una interfaz para crear un vehículo de transporte, y subclases específicas (Camión, Barco, Avión) implementarán este método para instanciar el transporte correcto.

https://dotnetfiddle.net/D9PGre
```c#
using System;

// Step 1: Define a common interface for all transport types
public interface ITransport
{
    void Deliver();
}

// Step 2: Implement concrete transport classes
public class Truck : ITransport
{
    public void Deliver()
    {
        Console.WriteLine("Delivering goods by road using a Truck.");
    }
}

public class Ship : ITransport
{
    public void Deliver()
    {
        Console.WriteLine("Delivering goods by sea using a Ship.");
    }
}

public class Airplane : ITransport
{
    public void Deliver()
    {
        Console.WriteLine("Delivering goods by air using an Airplane.");
    }
}

// Step 3: Create an abstract class for the logistics (factory method)
public abstract class Logistics
{
    // The factory method, which will be implemented by subclasses
    public abstract ITransport CreateTransport();

    // A common method that uses the transport to deliver goods
    public void PlanDelivery()
    {
        ITransport transport = CreateTransport();
        transport.Deliver();
    }
}

// Step 4: Implement concrete factory classes for each transport type
public class RoadLogistics : Logistics
{
    public override ITransport CreateTransport()
    {
        return new Truck();  // Creates a Truck for road transport
    }
}

public class SeaLogistics : Logistics
{
    public override ITransport CreateTransport()
    {
        return new Ship();  // Creates a Ship for sea transport
    }
}

public class AirLogistics : Logistics
{
    public override ITransport CreateTransport()
    {
        return new Airplane();  // Creates an Airplane for air transport
    }
}

// Step 5: Demonstrate the factory method pattern
class Program
{
    static void Main(string[] args)
    {
        // Plan a road delivery
        Logistics roadLogistics = new RoadLogistics();
        roadLogistics.PlanDelivery();

        // Plan a sea delivery
        Logistics seaLogistics = new SeaLogistics();
        seaLogistics.PlanDelivery();

        // Plan an air delivery
        Logistics airLogistics = new AirLogistics();
        airLogistics.PlanDelivery();
    }
}
```
## Salida
```c#
Delivering goods by road using a Truck.
Delivering goods by sea using a Ship.
Delivering goods by air using an Airplane.
```
### Analogía del mundo real

- RoadLogistics: Responsable del transporte por carretera, como los camiones para las entregas.
- SeaLogistics: Responsable del transporte marítimo, como los barcos para el transporte de mercancías a gran escala.
- AirLogistics: Responsable del transporte aéreo, como los aviones para entregas internacionales rápidas.

El patrón Factory Method permite que el sistema elija el método de transporte adecuado (camión, barco, avión) en función del tipo de logística sin vincular estrechamente el código a una clase específica. De esta manera, agregar nuevos tipos de logística (por ejemplo, transporte ferroviario) sería fácil creando una nueva subclase sin cambiar el código existente.

## Abstract Factory
### Escenario del mundo real
La mueblería puede crear distintos tipos de muebles para distintos estilos, como el estilo moderno y el victoriano. Cada juego de muebles contiene elementos como una silla y un sofá. Según el estilo, la fábrica producirá el tipo de mobiliario adecuado (moderno o victoriano).

En este escenario, la mueblería puede crear:

- Conjunto de muebles modernos: sillón moderno y sofá moderno
- Conjunto de muebles victorianos: sillón victoriano y sofá victoriano

El patrón Abstract Factory nos permitirá crear familias de objetos relacionados (conjuntos de muebles) sin especificar sus clases concretas.

https://dotnetfiddle.net/tepnxf
```c#
using System;

// Step 1: Define abstract products (interfaces for Chair and Sofa)
public interface IChair
{
    void SitOn();
}

public interface ISofa
{
    void LieOn();
}

// Step 2: Implement concrete products for Modern Furniture
public class ModernChair : IChair
{
    public void SitOn()
    {
        Console.WriteLine("Sitting on a modern chair.");
    }
}

public class ModernSofa : ISofa
{
    public void LieOn()
    {
        Console.WriteLine("Lying on a modern sofa.");
    }
}

// Step 3: Implement concrete products for Victorian Furniture
public class VictorianChair : IChair
{
    public void SitOn()
    {
        Console.WriteLine("Sitting on a Victorian chair.");
    }
}

public class VictorianSofa : ISofa
{
    public void LieOn()
    {
        Console.WriteLine("Lying on a Victorian sofa.");
    }
}

// Step 4: Define the abstract factory (furniture factory interface)
public interface IFurnitureFactory
{
    IChair CreateChair();
    ISofa CreateSofa();
}

// Step 5: Implement concrete factories for each furniture style
public class ModernFurnitureFactory : IFurnitureFactory
{
    public IChair CreateChair()
    {
        return new ModernChair();
    }

    public ISofa CreateSofa()
    {
        return new ModernSofa();
    }
}

public class VictorianFurnitureFactory : IFurnitureFactory
{
    public IChair CreateChair()
    {
        return new VictorianChair();
    }

    public ISofa CreateSofa()
    {
        return new VictorianSofa();
    }
}

// Step 6: Client code that works with factories and furniture
public class FurnitureStore
{
    private IChair _chair;
    private ISofa _sofa;

    public FurnitureStore(IFurnitureFactory factory)
    {
        _chair = factory.CreateChair();
        _sofa = factory.CreateSofa();
    }

    public void DescribeFurniture()
    {
        _chair.SitOn();
        _sofa.LieOn();
    }
}

// Step 7: Demonstrate the Abstract Factory Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Create modern furniture set
        IFurnitureFactory modernFactory = new ModernFurnitureFactory();
        FurnitureStore modernStore = new FurnitureStore(modernFactory);
        Console.WriteLine("Modern Furniture Set:");
        modernStore.DescribeFurniture();

        // Create Victorian furniture set
        IFurnitureFactory victorianFactory = new VictorianFurnitureFactory();
        FurnitureStore victorianStore = new FurnitureStore(victorianFactory);
        Console.WriteLine("\nVictorian Furniture Set:");
        victorianStore.DescribeFurniture();
    }
}
```
## Salida
```vbnet
Modern Furniture Set:
Sitting on a modern chair.
Lying on a modern sofa.

Victorian Furniture Set:
Sitting on a Victorian chair.
Lying on a Victorian sofa.
```
### Analogía del mundo real

- ModernFurnitureFactory produce muebles de estilo moderno, como sillas y sofás modernos.
- VictorianFurnitureFactory produce muebles de estilo victoriano, como sillas y sofás victorianos.

El patrón Abstract Factory es útil cuando un sistema necesita ser independiente de cómo se crean, componen o representan sus objetos. En este caso, el cliente (FurnitureStore) no necesita conocer las clases concretas de los muebles con los que está trabajando. En cambio, depende de las fábricas para proporcionar los tipos de objetos adecuados, lo que permite una fácil expansión a nuevos estilos en el futuro (por ejemplo, agregando ArtDecoFurnitureFactory).

## Builder
### Escenario del mundo real
El escenario es la construcción de una comida en un restaurante. La idea es crear un objeto complejo paso a paso. En este caso, el objeto es una comida que consta de diferentes partes, como el plato principal, la guarnición y la bebida. El cliente puede pedir diferentes combinaciones, como una comida vegetariana, una comida no vegetariana, etc.

En un restaurante:

Una comida vegetariana podría incluir: hamburguesa vegetariana como plato principal, papas fritas como guarnición y jugo como bebida.
Una comida no vegetariana podría incluir: hamburguesa de pollo como plato principal, papas fritas como guarnición y refresco como bebida.

El patrón Builder nos ayuda a construir estas diferentes variaciones de comidas paso a paso.

https://dotnetfiddle.net/rSvCRz
```c#
using System;

// Step 1: Define the product class (Meal)
public class Meal
{
    public string MainCourse { get; set; }
    public string SideDish { get; set; }
    public string Drink { get; set; }

    public void ShowMeal()
    {
        Console.WriteLine($"Main Course: {MainCourse}");
        Console.WriteLine($"Side Dish: {SideDish}");
        Console.WriteLine($"Drink: {Drink}");
    }
}

// Step 2: Define the builder interface (MealBuilder)
public interface IMealBuilder
{
    void BuildMainCourse();
    void BuildSideDish();
    void BuildDrink();
    Meal GetMeal();
}

// Step 3: Implement concrete builders for vegetarian and non-vegetarian meals
public class VegetarianMealBuilder : IMealBuilder
{
    private Meal _meal = new Meal();

    public void BuildMainCourse()
    {
        _meal.MainCourse = "Veggie Burger";
    }

    public void BuildSideDish()
    {
        _meal.SideDish = "Fries";
    }

    public void BuildDrink()
    {
        _meal.Drink = "Juice";
    }

    public Meal GetMeal()
    {
        return _meal;
    }
}

public class NonVegetarianMealBuilder : IMealBuilder
{
    private Meal _meal = new Meal();

    public void BuildMainCourse()
    {
        _meal.MainCourse = "Chicken Burger";
    }

    public void BuildSideDish()
    {
        _meal.SideDish = "Fries";
    }

    public void BuildDrink()
    {
        _meal.Drink = "Soda";
    }

    public Meal GetMeal()
    {
        return _meal;
    }
}

// Step 4: Define the director class to manage the meal-building process
public class MealDirector
{
    private IMealBuilder _mealBuilder;

    public MealDirector(IMealBuilder mealBuilder)
    {
        _mealBuilder = mealBuilder;
    }

    public void ConstructMeal()
    {
        _mealBuilder.BuildMainCourse();
        _mealBuilder.BuildSideDish();
        _mealBuilder.BuildDrink();
    }

    public Meal GetMeal()
    {
        return _mealBuilder.GetMeal();
    }
}

// Step 5: Demonstrate the Builder Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Creating a vegetarian meal
        IMealBuilder vegMealBuilder = new VegetarianMealBuilder();
        MealDirector director = new MealDirector(vegMealBuilder);
        director.ConstructMeal();
        Meal vegetarianMeal = director.GetMeal();

        Console.WriteLine("Vegetarian Meal:");
        vegetarianMeal.ShowMeal();

        // Creating a non-vegetarian meal
        IMealBuilder nonVegMealBuilder = new NonVegetarianMealBuilder();
        director = new MealDirector(nonVegMealBuilder);
        director.ConstructMeal();
        Meal nonVegetarianMeal = director.GetMeal();

        Console.WriteLine("\nNon-Vegetarian Meal:");
        nonVegetarianMeal.ShowMeal();
    }
}
```
## Salida
```yaml
Vegetarian Meal:
Main Course: Veggie Burger
Side Dish: Fries
Drink: Juice

Non-Vegetarian Meal:
Main Course: Chicken Burger
Side Dish: Fries
Drink: Soda

```
### Analogía del mundo real

- VegetarianMealBuilder: crea una comida vegetariana con una hamburguesa vegetariana, papas fritas y jugo.
- NonVegetarianMealBuilder: crea una comida no vegetariana con una hamburguesa de pollo, papas fritas y refresco.
- MealDirector: supervisa el proceso de creación de la comida. Se asegura de que la comida se cree paso a paso de acuerdo con el constructor que se utiliza.

El patrón Builder nos permite crear objetos complejos paso a paso y separa el proceso de construcción de la representación, lo que facilita la creación de diferentes variaciones de un objeto sin cambiar el código del cliente.

## Prototype
### Escenario del mundo real
En este escenario, la oficina podría manejar distintos tipos de documentos, como contratos, informes y cartas. Cada documento tiene un contenido y propiedades específicos y, en lugar de crear nuevas instancias desde cero, podemos clonar documentos existentes utilizando el patrón Prototipo.

La oficina maneja distintos tipos de documentos y los empleados necesitan con frecuencia crear copias de documentos existentes (por ejemplo, plantillas de contratos o formatos de informes).
Con el patrón Prototipo, podemos crear un nuevo documento clonando uno existente, lo que nos permite evitar la sobrecarga de crear nuevas instancias e inicializarlas manualmente.

https://dotnetfiddle.net/bApUwK
```c#
using System;

// Step 1: Define the Prototype interface (IDocument)
public interface IDocument
{
    IDocument Clone();
    void ShowDocumentDetails();
}

// Step 2: Implement Concrete Prototypes (Contract, Report, Letter)
public class Contract : IDocument
{
    public string PartyA { get; set; }
    public string PartyB { get; set; }
    public DateTime ContractDate { get; set; }

    public Contract(string partyA, string partyB, DateTime contractDate)
    {
        PartyA = partyA;
        PartyB = partyB;
        ContractDate = contractDate;
    }

    // Cloning the contract (deep copy)
    public IDocument Clone()
    {
        return new Contract(PartyA, PartyB, ContractDate);
    }

    public void ShowDocumentDetails()
    {
        Console.WriteLine($"Contract between {PartyA} and {PartyB}, signed on {ContractDate.ToShortDateString()}.");
    }
}

public class Report : IDocument
{
    public string Title { get; set; }
    public string Content { get; set; }

    public Report(string title, string content)
    {
        Title = title;
        Content = content;
    }

    // Cloning the report (deep copy)
    public IDocument Clone()
    {
        return new Report(Title, Content);
    }

    public void ShowDocumentDetails()
    {
        Console.WriteLine($"Report Title: {Title}\nContent: {Content}");
    }
}

public class Letter : IDocument
{
    public string Recipient { get; set; }
    public string Message { get; set; }

    public Letter(string recipient, string message)
    {
        Recipient = recipient;
        Message = message;
    }

    // Cloning the letter (deep copy)
    public IDocument Clone()
    {
        return new Letter(Recipient, Message);
    }

    public void ShowDocumentDetails()
    {
        Console.WriteLine($"Letter to {Recipient}: {Message}");
    }
}

// Step 3: Demonstrate the Prototype Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Original documents
        Contract contract = new Contract("Alice", "Bob", DateTime.Now);
        Report report = new Report("Q2 Sales Report", "The sales increased by 25% in Q2.");
        Letter letter = new Letter("John Doe", "Congratulations on your promotion!");

        // Clone the documents using the prototype pattern
        IDocument clonedContract = contract.Clone();
        IDocument clonedReport = report.Clone();
        IDocument clonedLetter = letter.Clone();

        // Display original and cloned document details
        Console.WriteLine("Original Contract:");
        contract.ShowDocumentDetails();
        Console.WriteLine("\nCloned Contract:");
        clonedContract.ShowDocumentDetails();

        Console.WriteLine("\nOriginal Report:");
        report.ShowDocumentDetails();
        Console.WriteLine("\nCloned Report:");
        clonedReport.ShowDocumentDetails();

        Console.WriteLine("\nOriginal Letter:");
        letter.ShowDocumentDetails();
        Console.WriteLine("\nCloned Letter:");
        clonedLetter.ShowDocumentDetails();
    }
}
```
## Salida
```yaml
Original Contract:
Contract between Alice and Bob, signed on 9/20/2024.

Cloned Contract:
Contract between Alice and Bob, signed on 9/20/2024.

Original Report:
Report Title: Q2 Sales Report
Content: The sales increased by 25% in Q2.

Cloned Report:
Report Title: Q2 Sales Report
Content: The sales increased by 25% in Q2.

Original Letter:
Letter to John Doe: Congratulations on your promotion!

Cloned Letter:
Letter to John Doe: Congratulations on your promotion!
```
### Analogía del mundo real:

Contrato (Contract): Un contrato entre dos partes que se puede clonar cuando se necesita un nuevo contrato con los mismos detalles.
Informe (Report): Un informe de la empresa que se puede clonar y modificar para diferentes períodos de informes.
Carta (Letter): Una carta que se puede clonar para enviar mensajes similares a diferentes destinatarios.

El patrón Prototipo es útil en situaciones en las que crear un objeto desde cero es costoso o complejo y, en su lugar, podemos crear una copia de un objeto existente. Esto es especialmente útil cuando se trata de la creación de objetos que involucran múltiples configuraciones o estados preestablecidos, como plantillas de documentos o planos.

## Adapter
### Escenario del mundo real
En este ejemplo, imagina que tienes un teléfono inteligente moderno que utiliza un puerto USB-C para cargar, pero solo tienes un cargador antiguo que utiliza un conector Micro-USB. El patrón de adaptador permite que el cargador Micro-USB se use con el puerto USB-C al convertir la interfaz del cargador en una que el teléfono comprenda.

Un teléfono inteligente moderno con un puerto USB-C.
Un cargador micro-USB antiguo.
Un adaptador que convierte el puerto micro-USB en USB-C, lo que permite que el cargador antiguo funcione con el nuevo teléfono.

https://dotnetfiddle.net/LUWdXT
```c#
using System;

// Step 1: Define the target interface (USB-C charging port)
public interface IUsbCCharger
{
    void ChargePhone();
}

// Step 2: Create the Adaptee class (Old Micro-USB charger)
public class MicroUsbCharger
{
    public void ChargeWithMicroUsb()
    {
        Console.WriteLine("Charging phone with Micro-USB charger.");
    }
}

// Step 3: Create the Adapter class (Micro-USB to USB-C adapter)
public class MicroUsbToUsbCAdapter : IUsbCCharger
{
    private MicroUsbCharger _microUsbCharger;

    // Adapter takes an instance of the old charger
    public MicroUsbToUsbCAdapter(MicroUsbCharger microUsbCharger)
    {
        _microUsbCharger = microUsbCharger;
    }

    // This method adapts the old charger's interface to the new one
    public void ChargePhone()
    {
        Console.WriteLine("Adapter converting Micro-USB to USB-C...");
        _microUsbCharger.ChargeWithMicroUsb();  // Delegate the call to the old charger
    }
}

// Step 4: Create the client class (Modern smartphone with USB-C port)
public class Smartphone
{
    private IUsbCCharger _charger;

    // Smartphone expects a USB-C charger
    public Smartphone(IUsbCCharger charger)
    {
        _charger = charger;
    }

    public void Charge()
    {
        Console.WriteLine("Connecting charger to the phone...");
        _charger.ChargePhone();
    }
}

// Step 5: Demonstrate the Adapter Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Old Micro-USB charger
        MicroUsbCharger oldCharger = new MicroUsbCharger();

        // Adapter converts Micro-USB to USB-C
        IUsbCCharger adapter = new MicroUsbToUsbCAdapter(oldCharger);

        // Smartphone expecting a USB-C charger
        Smartphone phone = new Smartphone(adapter);

        // Charge the phone using the adapter
        phone.Charge();
    }
}
```
## Salida
```vbnet
Connecting charger to the phone...
Adapter converting Micro-USB to USB-C...
Charging phone with Micro-USB charger.
```
### Analogía del mundo real

- Cargador antiguo (MicroUsbCharger): representa un cargador Micro-USB antiguo que solo funciona con dispositivos más antiguos.
- Teléfono inteligente moderno (Smartphone): representa un dispositivo moderno con un puerto de carga USB-C.
- Adaptador (MicroUsbToUsbCAdapter): convierte la interfaz del cargador Micro-USB en una interfaz USB-C, lo que la hace compatible con dispositivos modernos.

El patrón de adaptador se utiliza comúnmente cuando se necesita integrar sistemas antiguos con los nuevos. En este ejemplo, permite que un cargador antiguo funcione con un teléfono inteligente moderno al convertir su interfaz. El cliente (Smartphone) no sabe que está usando un adaptador.
## Bridge
### Escenario del mundo real
En este escenario, un control remoto podría operar varios dispositivos, como un televisor o una radio, pero el control remoto debe estar desacoplado de la implementación del dispositivo real. De esta manera, la misma interfaz de control remoto puede funcionar con varios tipos de dispositivos y se pueden agregar nuevos dispositivos sin modificar el control remoto.

- Control remoto: puede encender y apagar dispositivos y ajustar el volumen.
- Dispositivos: pueden ser televisores o radios, cada uno con su propia implementación específica para encender/apagar y ajustar el volumen.

El patrón Bridge ayuda a separar la abstracción (el control remoto) de la implementación (los dispositivos) para que puedan variar de forma independiente.

https://dotnetfiddle.net/VzYljG
```c#
using System;

// Step 1: Define the implementation interface (Device)
public interface IDevice
{
    void PowerOn();
    void PowerOff();
    void SetVolume(int percent);
    bool IsPoweredOn();
}

// Step 2: Implement concrete devices (TV and Radio)
public class TV : IDevice
{
    private bool _powerOn = false;
    private int _volume = 50;

    public void PowerOn()
    {
        _powerOn = true;
        Console.WriteLine("TV is now ON.");
    }

    public void PowerOff()
    {
        _powerOn = false;
        Console.WriteLine("TV is now OFF.");
    }

    public void SetVolume(int percent)
    {
        _volume = percent;
        Console.WriteLine($"TV volume set to {_volume}%.");
    }

    public bool IsPoweredOn()
    {
        return _powerOn;
    }
}

public class Radio : IDevice
{
    private bool _powerOn = false;
    private int _volume = 30;

    public void PowerOn()
    {
        _powerOn = true;
        Console.WriteLine("Radio is now ON.");
    }

    public void PowerOff()
    {
        _powerOn = false;
        Console.WriteLine("Radio is now OFF.");
    }

    public void SetVolume(int percent)
    {
        _volume = percent;
        Console.WriteLine($"Radio volume set to {_volume}%.");
    }

    public bool IsPoweredOn()
    {
        return _powerOn;
    }
}

// Step 3: Define the abstraction class (RemoteControl)
public class RemoteControl
{
    protected IDevice _device;

    // The remote control takes a device to control
    public RemoteControl(IDevice device)
    {
        _device = device;
    }

    public void TogglePower()
    {
        if (_device.IsPoweredOn())
        {
            _device.PowerOff();
        }
        else
        {
            _device.PowerOn();
        }
    }

    public void VolumeUp()
    {
        Console.WriteLine("Volume up button pressed.");
    }

    public void VolumeDown()
    {
        Console.WriteLine("Volume down button pressed.");
    }
}

// Step 4: Extend the abstraction to add advanced features (AdvancedRemoteControl)
public class AdvancedRemoteControl : RemoteControl
{
    public AdvancedRemoteControl(IDevice device) : base(device) { }

    // Advanced remote can directly set volume
    public void SetVolume(int percent)
    {
        _device.SetVolume(percent);
    }
}

// Step 5: Demonstrate the Bridge Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Use remote with a TV
        IDevice tv = new TV();
        RemoteControl tvRemote = new RemoteControl(tv);

        Console.WriteLine("Using TV with basic remote:");
        tvRemote.TogglePower();  // Turns on the TV
        tvRemote.TogglePower();  // Turns off the TV

        // Use advanced remote with a Radio
        IDevice radio = new Radio();
        AdvancedRemoteControl radioRemote = new AdvancedRemoteControl(radio);

        Console.WriteLine("\nUsing Radio with advanced remote:");
        radioRemote.TogglePower();  // Turns on the Radio
        radioRemote.SetVolume(70);  // Sets the radio volume to 70%
        radioRemote.TogglePower();  // Turns off the Radio
    }
}
```
## Salida
```vbnet
Using TV with basic remote:
TV is now ON.
TV is now OFF.

Using Radio with advanced remote:
Radio is now ON.
Radio volume set to 70%.
Radio is now OFF.
```
### Analogía del mundo real

- Dispositivos (TV, radio): representa varios dispositivos como un televisor y una radio, que tienen sus propios comportamientos únicos para el control de volumen y encendido.
- Control remoto: representa un control remoto general que puede funcionar con cualquier dispositivo mediante una interfaz común (IDevice).
- Control remoto avanzado: extiende la funcionalidad del control remoto básico con funciones adicionales, como configurar directamente el volumen.

El patrón Bridge permite que la abstracción (Control remoto) y la implementación (Dispositivo) varíen de forma independiente. Puede extender fácilmente la funcionalidad del control remoto o agregar nuevos dispositivos (por ejemplo, un sistema de sonido) sin modificar el código existente. Este patrón es útil cuando desea desacoplar una abstracción de su implementación para que ambas puedan evolucionar por separado.
## Composite
### Escenario del mundo real
<p>En este escenario, una organización tiene empleados que pueden ser colaboradores individuales o gerentes que supervisan a otros empleados. El patrón compuesto nos ayuda a tratar de manera uniforme tanto a los empleados individuales como a los grupos de empleados (gerentes y sus subordinados).</p>

- Empleado: puede ser un trabajador individual o un gerente.
- Gerente: un gerente puede tener subordinados (otros empleados o gerentes).
- Patrón compuesto: nos permite tratar tanto a los empleados individuales como a los grupos de empleados (gerentes con subordinados) de la misma manera.

https://dotnetfiddle.net/h5oAIY
```c#
using System;
using System.Collections.Generic;

// Step 1: Define the Component interface (Employee)
public interface IEmployee
{
    string GetDetails();
}

// Step 2: Implement the Leaf class (Individual Employee)
public class Employee : IEmployee
{
    private string _name;
    private string _position;

    public Employee(string name, string position)
    {
        _name = name;
        _position = position;
    }

    public string GetDetails()
    {
        return $"{_name} - {_position}";
    }
}

// Step 3: Implement the Composite class (Manager with subordinates)
public class Manager : IEmployee
{
    private string _name;
    private string _position;
    private List<IEmployee> _subordinates = new List<IEmployee>();

    public Manager(string name, string position)
    {
        _name = name;
        _position = position;
    }

    // Add an employee (either individual or manager) to the subordinates list
    public void AddSubordinate(IEmployee employee)
    {
        _subordinates.Add(employee);
    }

    // Remove an employee from the subordinates list
    public void RemoveSubordinate(IEmployee employee)
    {
        _subordinates.Remove(employee);
    }

    // Get details of the manager and all subordinates recursively
    public string GetDetails()
    {
        string details = $"{_name} - {_position}\n";
        foreach (var subordinate in _subordinates)
        {
            details += "  " + subordinate.GetDetails() + "\n";
        }
        return details.Trim();
    }
}

// Step 4: Demonstrate the Composite Pattern in action
class Program
{
    static void Main(string[] args)
    {
        // Create individual employees
        Employee developer1 = new Employee("Alice", "Senior Developer");
        Employee developer2 = new Employee("Bob", "Junior Developer");
        Employee designer = new Employee("Charlie", "Designer");

        // Create managers
        Manager devManager = new Manager("David", "Development Manager");
        Manager generalManager = new Manager("Eve", "General Manager");

        // Development manager manages developers
        devManager.AddSubordinate(developer1);
        devManager.AddSubordinate(developer2);

        // General manager manages development manager and designer
        generalManager.AddSubordinate(devManager);
        generalManager.AddSubordinate(designer);

        // Get details of the entire organization
        Console.WriteLine("Organization Structure:");
        Console.WriteLine(generalManager.GetDetails());
    }
}
```
## Salida
```markdown
Organization Structure:
Eve - General Manager
  David - Development Manager
    Alice - Senior Developer
    Bob - Junior Developer
  Charlie - Designer
```
### Analogía del mundo real

- Empleado (Employee): representa a un trabajador individual, como un desarrollador o diseñador.
- Gerente (Manager): representa a un gerente que supervisa a otros empleados, incluidos tanto los colaboradores individuales como otros gerentes.
- Estructura de la organización: al utilizar el patrón compuesto, tanto los gerentes como los empleados individuales pueden ser tratados de manera uniforme, lo que permite una fácil manipulación de la estructura jerárquica (agregar o eliminar subordinados, etc.).

El patrón compuesto nos permite componer objetos en estructuras de árbol para representar jerarquías de partes y todo. Permite a los clientes tratar objetos individuales y composiciones de objetos de manera uniforme, lo que es particularmente útil en escenarios como estructuras de organización donde existen tanto individuos como grupos (gerentes con equipos).
