# Integrating AI and ML in .NET Core with ML.NET

* ML.NET is Microsoft's cross-platform, open-source ML framework that brings AI and ML to .NET developers.
* ML.NET lets you build, train, and deploy ML models that can help you build high-quality intelligent applications that can cater to the needs of today's businesses. 
* Most popular ML frameworks include - TensorFlow, PyTorch, Microsoft CNTK, Amazon Machine Learning
* use ML.NET to do most of the things you can do with Azure Cognitive Services. 

* Do with ML.NET at a glance:
    * Regression Helps predict numerical values (i.e., item prices, population, etc.)
    * Classification: Sorts data into categories (i.e., spam detection, fraud detection)
    * Clustering: Groups similar data points (i.e., customer segmentation)
    * Recommendation: Provides suggestions on products or content
    * Anomaly detection: Identifies unusual patterns in data (i.e., fraud detection)
    * Time series forecasting: Predicts future values based on a set of historical data (i.e., stock market predictions)
    * Model evaluation: Assesses model performance based on their accuracy, precision, etc.

## A typical ML workflow encompasses the following phases:
   * Problem statement: Define the business problem and then analyze how best to apply machine learning to solve the problem.
   * Data collection: The first phase in this workflow retrieves data from various data sources.
   * Data preparation: The data is cleaned to remove any errors and make the data ready for analysis and modeling.
   * Model selection and training: Select the appropriate ML algorithm (i.e., decision tree, logistic regression, neural network, etc.) and train the model using the dataset so that it knows the patterns and relationships in the data to be able to make predictions.
   * Evaluation: Test the trained model to measure performance using metrics such as precision, accuracy, etc.
   * Deployment: The trained and validated model is integrated into the production systems (i.e., business applications built using ASP.NET Core, mobile apps, desktop apps, etc.) so that it can generate predictions or automate decisions in real world environments.
   * Monitoring and maintenance: Monitor the model performance, and retrain and update the model as needed to keep it accurate and relevant over time.

## Understanding the ML.NET Components
   * MLContext 
     * It represents the starting point in any ML.NET workflow and provides a single context to manage data loading, processing, transformation, model training, evaluation, and model persistence.
   * IDataView 
     * In ML.NET, data is represented using an abstraction named IDataView. 
     * It provides an efficient and schema-aware representation of both training and prediction data. 
     * IDataView objects are evaluated lazily for better performance and resource efficiency.
   * Data loaders and data converters
     * use File Loaders for retrieving data available in text, binary, and image files, 
     * or use the Database Loader to load data from a database and then train the datasets. 
     * Data Transformers are used to clean and transform the data in a format that's understandable by ML.NET.
   * Model training
     * It is a process used to build and train the models. 
     * ML.NET takes advantage of one or more of the built-in algorithms for training models - Clustering, Classification, Regression, Time series analysis, Anomaly detection
   * Model evaluation
     * Before deploy a trained model into the production environment, make sure that it's high-quality so that it can make the right predictions using model evaluation. 
     * ML.NET has multiple evaluators to evaluate the accuracy, precision, and performance of trained models. 
     * These model evaluators take advantage of several metrics to measure how well your model is performing in real-time. 
     * The most commonly used metrics in this regard are:
        * Binary classification: Accuracy, precision, recall, F1Score, AreaUnderROC
        * Regression: RMSE (root mean squared error), RSquared
        * Clustering: Normalized mutual information

## Create a New .NET 10 Console Application in VS 2022:

Start the Visual Studio 2022 Preview IDE.
In the Create a new project window, select Console App and click Next to move on.
Specify the project name and the path where it should be created in the Configure your new project window.
If you want the solution file and project to be created in the same directory, optionally check the Place solution and project in the same directory checkbox. Click Next to move on.
In the Additional information screen, specify the target framework as .NET 10.0 (Preview).
Click Create to complete the process.
Add Machine Learning Capabilities to Your Project
Next, right-click on the project in the Solution Explorer window and then select Add > Machine Learning Model as shown in Figure 1.

Figure 1: Adding machine learning support to the project
Figure 1: Adding machine learning support to the project
In the Add New Item window that's shown next, ensure that you've selected the Machine Learning Model (ML.NET) template. Then, specify a name for the model config file and click on the Add button. Note that the extension of this file should be mbconfig. The mbconfig file, as the name suggests, is a configuration file based on JSON format that contains the state and configuration information of your machine learning model project.

In the Add New Item window that's shown next, ensure that you've selected the Machine Learning Model (ML.NET) template. Then, specify a name for the ML model config file and click on the Add button, as shown in Figure 2.

Figure 2: Adding the ML Model to the project
Figure 2: Adding the ML Model to the project
Let's now implement a typical ML workflow in a step-by-step manner.

Creating a Model Using the ML.NET Model Builder
The ML.NET Model Builder is a graphical extension that you can use inside your Visual Studio IDE, and which allows .NET developers to build, train, and deploy custom machine learning models without needing to have any prior knowledge of machine learning.

Here are some of its key features:

Graphical interface: The ML.NET Model Builder provides a wizard-based GUI tool, so you don't need to write code or know ML to start building models—just provide your data and choose your scenario.
Automated machine learning (AutoML): The Model Builder uses AutoML to try different ML algorithms, settings, and parameters to find the best model for you.
Scenario selection: The Model Builder provides support for common ML tasks like classification, value (regression) prediction, recommendation, forecasting, image classification, and more. You choose what you want to predict, and Model Builder takes care of the rest.
Data connection: The Model Builder can load data from CSV, TXT, and SQL Server databases.
Code generation: After successful model training, the Model Builder generates code files for model training and consumption.
Integration and deployment: You can add the generated code and models to any of your .NET projects.
Free: You don't incur any additional costs to use the Model Builder tool because it's freely available by default in your Visual Studio IDE.
When you're using the Model Builder, follow these steps to build and train your ML models:

Choose an ML scenario.
Select your data.
Train the model.
Evaluate.
Predict.
Generate code.
Figure 3 illustrates the complete ML workflow.

Figure 3: A typical ML workflow
Figure 3: A typical ML workflow
Choose a Machine Learning Scenario
To build a model using the model builder, start by selecting a machine learning scenario. In this example, select the Data classification scenario from the Model Builder window from the Select a scenario screen, as illustrated in Figure 4.

Figure 4: Selecting a machine learning scenario
Figure 4: Selecting a machine learning scenario
Select the Training Environment
Once you've selected the scenario, choose the training environment. Select Local (CPU) as the training environment for your model, as shown in Figure 5.

Figure 5: Select a training environment for your model.
Figure 5: Select a training environment for your model.
Adding Data
To add data for your model, select the data source type for your input data (i.e., the CSV file in the local file system in this example) and then load the CSV file. Additionally, also select the column you want to predict, i.e., select the desired column to use for prediction by specifying the Column to predict (Label). In this example, I've selected the Sentiment column, as shown in Figure 6.

Figure 6: Add data for your model.
Figure 6: Add data for your model.
You can use the advanced options window to modify column settings, schema information, and configure test data.

Train the Model
In the next step, you need to train the model with the data you loaded from the CSV file. The Model Builder is adept at training the model automatically but you can also customize it by specifying the training duration and a few other additional parameters via the Advanced training options screen. Once you've specified the custom training duration and then clicked on the Train again button, the training results will be displayed, as shown in Figure 7.

Figure 7: The training results are displayed upon changing the training duration and clicking on the Train again button
Figure 7: The training results are displayed upon changing the training duration and clicking on the Train again button
Evaluate the Model
Once you've trained the model successfully, you can evaluate the model to make predictions on a piece of test data and then access the results. The Model Builder provides an intuitive interface to enter the input data and then see how the model can predict data, as shown in Figure 8.

Figure 8: The ML model in action
Figure 8: The ML model in action
Consume the Model
Finally, it's time to consume the model. The Model Builder helps you generate two types of applications to consume the model: either generate a Console application or create a Web Api application. Refer to Figure 9, which illustrates how the Model Builder uses in-built project templates to generate these applications for you.

Figure 9: Consuming the model
Figure 9: Consuming the model
The Model Builder also generates sample source code as a reference to consume the model. For example, you can use the following piece of code to load sample data:

 
var sampleData = new MyFirstMLModel.ModelInput()
    {
        SentimentText = @"The product is fine but it did not 
                          satisfy my expectations.",
    };
The following code snippet shows how you can load the model and then invoke the Predict method on the model instance to predict output based on the input data provided:

 
var result = MyFirstMLModel.Predict(sampleData);

If you choose to generate your model consumer application using the project in-built templates, the new project (you'll need to choose between a console app or a Web Api app, as shown in Figure 9) will be created and added to the solution currently in your Visual Studio IDE. Remember, you can always alter the generated code based on your requirements.

In the sections that follow, I'll examine how to take advantage of ML.NET to predict product prices in an ASP.NET Core application.

Advertisement

Implementing a Product Price Prediction API Using ML.NET and ASP.NET Core
In this section, you'll implement a real-life application that uses machine learning algorithms to predict product prices. To build this real-life application, follow these steps:

Create the database.
Create an ASP.NET Core Web API project.
Add the model to the project.
Train and evaluate the model.
Retrain the model.
Predict the product price.
Figure 10 illustrates the complete flow of how the components of the application work.

Figure 10: The complete flow of the real-life application
Figure 10: The complete flow of the real-life application
Create a New ASP.NET Core 10 Project in Visual Studio 2022
You can create a project in Visual Studio 2022 in several ways, such as from the Visual Studio 2022 Developer Command Prompt or by launching the Visual Studio 2022 IDE. When you launch Visual Studio 2022, you'll see the Start window. You can choose Continue without code to launch the main screen of the Visual Studio 2022 IDE.

Now that you know the basics, let's start setting up the project. To create a new ASP.NET Core 8 Project in Visual Studio 2022:

Start the Visual Studio 2022 IDE.
In the Create a new project window, select ASP.NET Core Web API and click Next to move on.
Specify the project name as ASPNETCoreMLDemo and the path where it should be created in the Configure your new project window.
If you want the solution file and project to be created in the same directory, you can optionally check the Place solution and project in the same directory checkbox. Click Next to move on.
In the next screen, specify the target framework and authentication type. Ensure that the Configure for HTTPS, Enable Docker Support, Do not use top-level statements, and the Enable OpenAPI support checkboxes are unchecked because you won't use any of these in this example.
Remember to leave the Use controllers checkbox checked because you won't use minimal API in this example.
Click Create to complete the process.
A new ASP.NET Core Web API project is created. You'll use this project to implement the CQRS pattern in ASP.NET Core and C#.

Create the Database
In this example, you'll create a database named StockManagementSystem that contains one table called Product. You'll write a SQL script that generates several product records (about 100 records) with the following fields:

Product_Id (integer, primary key)
Product_Code (varchar 5)
Product_Type (varchar 25)
Region_Code (varchar 2)
Price (decimal)
Stock_Quantity (integer)
The records in the Product database table will be generated based on the following assumptions:

There cannot be two records having the same combination of region code, product code, and price.
The price of the product with product code P0002 will be more if the region code is R1.
The price of the product P0001 will be more if the region code is R2.
The price of the product P0003 will be more if the region code is R1 or R2.
The price of the product P0004 will be more if the region code is R3 or R4.
There will only be four region codes: R1, R2, R3, and R4. Typical product codes will be P0001, P0002, P0003, and P0004.
The same product should be available in multiple regions, i.e., P001 should be available in R2, R3, and R4.
The price of a product will vary in different regions, i.e., if the region codes change, the product price will also change.
As an example, a laptop can have a price of 1500 in R2 but it can be 1450 in R3 and 1475 in R4, and so on.
Typical product types can be Laptop, Furniture, Smart Phone, etc.
Each product type will have a distinct product code.
The following code snippet shows how you can create a database table named Products.

 
CREATE TABLE dbo.Products (
    Product_Id INT IDENTITY(1, 1) PRIMARY KEY,
    Product_Code VARCHAR(5) NOT NULL,
    Product_Type VARCHAR(25) NOT NULL,
    Region_Code VARCHAR(2) NOT NULL,
    Product_Price DECIMAL(10, 2) NOT NULL,
    Stock_Quantity INT NOT NULL
);
The following piece of code shows how you can define a unique constraint so that no two records in this table have the same combination of Product_Code, Region_Code, and Product_Price.

 
ALTER TABLE dbo.Products
ADD CONSTRAINT UQ_ProductRegionPrice 
UNIQUE(Product_Code, Region_Code, Product_Price);
GO
GO
The complete script for creating the database is given in Listing 1.

Listing 1: The Database Script
-- Drop the Product database table if already it exists
IF OBJECT_ID('dbo.Product', 'U') IS NOT NULL
    DROP TABLE dbo.Product;
GO

-- Create the Product database table
CREATE TABLE dbo.Product
(
    Product_Id INT IDENTITY(1,1) PRIMARY KEY,
    Product_Code VARCHAR(5) NOT NULL,
    Product_Type VARCHAR(25) NOT NULL,
    Region_Code VARCHAR(2) NOT NULL,
    Product_Price DECIMAL(10,2) NOT NULL
);
GO

;WITH Numbers AS
(
    SELECT TOP (100) ROW_NUMBER()
        OVER (ORDER BY (SELECT NULL)) AS N
    FROM sys.all_objects
)
INSERT INTO dbo.Product
    (Product_Code, Product_Type, Region_Code, Product_Price)
SELECT
    -- Map product codes
    CASE ((N-1) % 4)
        WHEN 0 THEN 'P0001'
        WHEN 1 THEN 'P0002'
        WHEN 2 THEN 'P0003'
        WHEN 3 THEN 'P0004'
    END AS Product_Code,

    -- Map product types
    CASE ((N-1) % 4)
        WHEN 0 THEN 'Laptop'
        WHEN 1 THEN 'Furniture'
        WHEN 2 THEN 'Smart Phone'
        WHEN 3 THEN 'Tablet'
    END AS Product_Type,

    'R' + CAST(((N-1) % 4) + 1 AS VARCHAR(1)) AS Region_Code,

    -- Generate the Product price logic
    CASE
        -- P0002 more in R1
        WHEN ((N-1) % 4) = 1 AND ((N-1) % 4) + 1 = 1
            THEN 1600 + (N % 50)

        -- P0001 more in R2
        WHEN ((N-1) % 4) = 0 AND ((N-1) % 4) + 1 = 2
            THEN 1550 + (N % 40)

        -- P0003 more in R1 or R2
        WHEN ((N-1) % 4) = 2 AND (((N-1) % 4) + 1 IN (1, 2))
            THEN 1400 + (N % 60)

        -- P0004 more in R3 or R4
        WHEN ((N-1) % 4) = 3 AND (((N-1) % 4) + 1 IN (3, 4))
            THEN 1350 + (N % 30)

        -- Base price with variation
        ELSE 1200 + (N % 100)
    END AS Price
FROM Numbers;
GO

-- Verify data
SELECT * FROM dbo.Product
ORDER BY Product_Id;

Install the NuGet Packages
The next step is to install the necessary NuGet Package(s) for the application. In this application, you'll need EF Core and ML.NET. To install the required packages into your project, right-click on the solution and then select Manage NuGet Packages for Solution….

Once the window pops up, search for the NuGet packages to add to your project. To do this, type in Microsoft.EntityFrameworkCore, Microsoft.EntityFrameworkCore.Design, Microsoft.EntityFrameworkCore.Tools, Microsoft.EntityFrameworkCore.SqlServer, and Microsoft.ML in the search box and install them one after the other. Alternatively, you can type the commands shown below at the NuGet Package Manager Command Prompt:

 
PM> Install-Package Microsoft.EntityFrameworkCore

PM> Install-Package Microsoft.EntityFrameworkCore.Design

PM> Install-Package Microsoft.EntityFrameworkCore.Tools

PM> Install-Package Microsoft.EntityFrameworkCore.SqlServer

PM> Install-Package Microsoft.ML
Create the Model Classes
Create a new class called Product in a file named Product.cs inside the Models folder and write the following code in there:

 
public class Product
{
    public int Product_Id
    {
    }

    public string Product_Code
    {
        get;
        set;
    } = null!;

    public string Product_Type
    {
        get;
        set;
    } = null!;

    public string Region_Code
    {
        get;
        set;
    } = null!;

    public decimal Product_Price
    {
        get;
        set;
    }

    public int Stock_Quantity
    {
        get;
        set;
    }
}

You'll create two more model classes, ProductPriceInput and ProductPricePrediction classes, as shown below.

 
public class ProductPriceInput
{
    public string Product_Code { get; set; }
    public string Region_Code { get; set; }
    public float Product_Price { get; set; }
}

public class ProductPriceOutput
{
    public float Product_Price { get; set; }
    public float Score { get; set; }
}
ProductPriceInput will be used to store a combination of region code and product code data and pass it as a parameter to the HttpPost method in the ProductController, and ProductPricePrediction will be used to store the result of the prediction.

Create the Data Context
In Entity Framework Core (EF Core), a data context is a component used by an application to interact with the database and manage database connections, and to query and persist data in the database. Let's now create the data context class to enable the application to interact with the database to perform CRUD (Create, Read, Update, and Delete) operations.

To do this, create a new class named ProductDbContext that extends the DbContext class of EF Core and write the following code in there:

 
public class ProductDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        base.OnConfiguring(optionsBuilder);
    }
}

In the preceding piece of code, the statement base.OnConfiguring(optionsBuilder) calls the OnConfiguring method of the base class of your ProductDbContext. You can specify your database connection string in the OnConfiguring overloaded method of the ProductDbContext class. However, in this implementation, you'll store the database connection settings in the AppSettings.json file and read it in the Program.cs file to establish a database connection. The following code snippet illustrates how you can define a public constructor for your data context class.

 
public ProductDbContext(DbContextOptions<ProductDbContext> options, 
    IConfiguration configuration) : base(options)
{
    _configuration = configuration;
}

Register the ProductDb data context instance as a service with the services container of ASP.NET Core using the following piece of code in the Program.cs file:

 
builder.Services.AddDbContext<ProductDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration["ConnectionStrings:DefaultConnection"]);
});
If your application needs to perform multiple units of work, it's advisable to use a DbContext factory instead. To do this, register a factory by calling the AddDbContextFactory method in the Program.cs file of your project, as shown in the following code example:

 
builder.Services.AddDbContextFactory<ProductDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration["ConnectionStrings: DefaultConnection"]
    );
});
The complete source code of ProductDbContext class is given in Listing 2.

Listing 2: The ProductDbContext class
public class ProductDbContext : DbContext
{
    private readonly IConfiguration _configuration;

    public ProductDbContext(DbContextOptions<ProductDbContext> options, 
        IConfiguration configuration) : base(options)
    {
        _configuration = configuration;
    }

    protected override void OnConfiguring(DbContextOptionsBuilder 
        optionsBuilder)
    {
        _ = optionsBuilder
            .UseSqlServer(_configuration.GetConnectionString(
                "DefaultConnection"))
            .EnableSensitiveDataLogging();
    }

    public DbSet<Product> Products { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Product>().ToTable("Product");
        modelBuilder.Entity<Product>().HasKey(p => p.Product_Id);
    }
}

Create the ProductRepository Class
A repository class is an implementation of the Repository design pattern and is one that manages data access. The application takes advantage of the repository instance to perform CRUD operations against the database. In this example, you'll use the repository to load data only.

Create a new interface named IProductRepository in a file having the same name and write the following code in there:

 
public interface IProductRepository
{
}
Now, create a new class named ProductRepository in a file having the same name with a .cs extension. The ProductRepository class should extend the IProductRepository interface and implement its members.

 
public class ProductRepository : IProductRepository
{
}

Here is how the IProductRepository interface should look with all its members in there:

 
public interface IProductRepository
{
    public Task<List<Product>> GetAllProductsAsync();

    public Task<Product> GetProductByIdAsync(int id);

    public Task<List<Product>> GetProductsByCodeAndRegionAsync(string productCode, 
        string regionCode);
}

The ProductRepository class, illustrated in the code snippet below, implements the methods of the IProductRepository interface.

 
public async Task<List<Product>>
GetAllProductsAsync()
{
    return await await _productDbContext.Products.ToListAsync();
}

public async Task<Product>
GetProductByIdAsync(int id)
{
    return await _productDbContext.Products.FirstOrDefaultAsync(
        p => p.Product_Id == id);
}

public async Task<List<Product>>
GetProductsByCodeAndRegionAsync(string productCode, string regionCode)
{
    return await _productDbContext.Products
        .Where(p => p.Product_Code == productCode && p.Region_Code == regionCode)
        .ToListAsync();
}
The complete source code of the ProductRepository class is given in Listing 3.

Listing 3: The ProductRepository class
public class ProductRepository : IProductRepository
{
    private readonly ProductDbContext _productDbContext;

    public ProductRepository(ProductDbContext productDbContext)
    {
        _productDbContext = productDbContext;
    }

    public async Task<List<Product>> GetAllProductsAsync()
    {
        return await _productDbContext.Products.ToListAsync();
    }

    public async Task<Product> GetProductByIdAsync(int id)
    {
        return await _productDbContext.Products.FirstOrDefaultAsync(
            p => p.Product_Id == id);
    }

    public async Task<List<Product>> GetProductsByCodeAndRegionAsync(
        string productCode, string regionCode)
    {
        return await _productDbContext.Products
            .Where(p => p.Product_Code == productCode && p.Region_Code == 
                regionCode)
            .ToListAsync();
    }
}

Create the ProductPricePredictionService Class
Next, create a class named ProductPricePredictionService that will contain the actual logic for leveraging ML.NET to predict the product price. To do this, create a new class named ProductPricePredictionService in a file named ProductPricePredictionService.cs and replace the generated code using the code snippet given below:

 
public class ProductPricePredictionService : IProductPricePredictionService
{
    private readonly IProductRepository _productRepository;

    public ProductPricePredictionService(IProductRepository repository)
    {
        _productRepository = repository;
    }

    public async Task<double> PredictPrice(string productCode, string regionCode)
    {
        //Not yet implemented
    }
}

In the preceding code snippet, an instance of type IProductRepository is passed to the constructor of the ProductPricePredictionService class using constructor injection. The ProductPricePredictionService class will be used by the ProductController class to return prediction results.

You now need to load the data from the database, transform the data, and create a prediction engine instance to predict data based on input values. The following code snippet shows how this can be achieved.

 
public async Task<double> PredictPrice(string productCode, string regionCode)
{
    var mlContext = new MLContext();

    var products = await _productRepository.GetAllProductsAsync();

    var data = products.Select(p => new ProductPriceInput
    {
        Product_Code = p.Product_Code,
        Region_Code = p.Region_Code,
        Product_Price = Convert.ToSingle(p.Product_Price)
    }).ToList();

    var dataView = mlContext.Data.LoadFromEnumerable(data);

    var pipeline = mlContext.Transforms.Text.FeaturizeText(inputColumnName: 
            @"Product_Code", outputColumnName: @"Product_Code")
        .Append(mlContext.Transforms.Text.FeaturizeText(inputColumnName: 
            @"Region_Code", outputColumnName: @"Region_Code"))
        .Append(mlContext.Transforms.Concatenate(
            @"Features", new[] { @"Product_Code", @"Region_Code" }))
        .Append(mlContext.Transforms.CopyColumns("Label", "Product_Price"))
        .Append(mlContext.Regression.Trainers.Sdca());

    var model = pipeline.Fit(dataView);

    PredictionEngine<ProductPriceInput, ProductPriceOutput> _predictionEngine =
        mlContext.Model.CreatePredictionEngine<ProductPriceInput, 
        ProductPriceOutput>(model);

    ProductPriceInput sampleData = new ProductPriceInput()
    {
        Product_Code = productCode,
        Region_Code = regionCode
    };

    var predictionResult = _predictionEngine.Predict(sampleData);

    return await Task.FromResult(Math.Round(predictionResult.Score, 2));
}
The complete source code of the ProductPricePredictionService class is given in Listing 4.

Listing 4: The ProductPricePredictionService class
public class 
ProductPricePredictionService : IProductPricePredictionService
{
    private readonly IProductRepository _productRepository;
    public ProductPricePredictionService(IProductRepository repository)
    {
        _productRepository = repository;
    }


    public async Task<double> PredictPrice(string productCode, string regionCode)
    {
        var mlContext = new MLContext();
        var products = await _productRepository.GetAllProductsAsync();
        var data = products.Select(p => new ProductPriceInput
        {
            Product_Code = p.Product_Code,
            Region_Code = p.Region_Code,
            Product_Price = Convert.ToSingle(p.Product_Price)
        }).ToList();

        var dataView = mlContext.Data.LoadFromEnumerable(data);

        var pipeline = mlContext.Transforms.Text.FeaturizeText(inputColumnName: 
                @"Product_Code", outputColumnName: @"Product_Code")
            .Append(mlContext.Transforms.Text.FeaturizeText(inputColumnName: 
                @"Region_Code", outputColumnName: @"Region_Code"))
            .Append(mlContext.Transforms.Concatenate
                (@"Features", new[] { @"Product_Code", @"Region_Code" })
            .Append(mlContext.Transforms.CopyColumns("Label", "Product_Price"))
            .Append(mlContext.Regression.Trainers.Sdca()));

        var model = pipeline.Fit(dataView);

        PredictionEngine<ProductPriceInput, ProductPriceOutput> 
            _predictionEngine = mlContext.Model
                .CreatePredictionEngine<ProductPriceInput, 
                ProductPriceOutput>(model);

        ProductPriceInput sampleData = new ProductPriceInput()
        {
            Product_Code = productCode,
            Region_Code = regionCode
        };

        var predictionResult = _predictionEngine.Predict(sampleData);

        return await Task.FromResult(Math.Round(predictionResult.Score, 2));
    }
}

Register the Services
To be able to leverage dependency injection to access instances of the repository and the service class you created earlier, register these instances with the service collection by writing the following piece of code in the Program.cs file:

 
builder.Services.AddScoped<IProductRepository, ProductRepository>();
builder.Services.AddScoped<IProductPricePredictionService, 
    ProductPricePredictionService>();
Also register the ProductDbContext with the service collection so that you can access an instance of the ProductDbContext class in your product repository class or other classes if needed.

 
builder.Services.AddDbContext<ProductDbContext>(options =>
{
    options.UseSqlServer(
        builder.Configuration["ConnectionStrings:DefaultConnection"]);
});
The complete source code of the Program.cs file is given in Listing 5.

Listing 5: The Program.cs file
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.builder.Services.AddControllers();

var connectionString = builder.Configuration.GetConnectionString(
    "DefaultConnection");
builder.Services.AddDbContext<ProductDbContext>(options =>
{
    options.UseSqlServer(builder.Configuration
        ["ConnectionStrings:DefaultConnection"]);
});

builder.Services.AddScoped<IProductRepository, ProductRepository>();
builder.Services.AddScoped<IProductPricePredictionService, 
    ProductPricePredictionService>();
    
var app = builder.Build();

// Configure the HTTP request pipeline.
app.UseHttpsRedirection();

//app.UseAuthorization();
app.MapControllers();

app.Run();
Now, create a new controller named ProductController in the Controllers folder of the project. The following code snippet shows how you can take advantage of constructor injection to pass an instance of type IProductPredictionService interface using the constructor of the ProductController class.

 
public class ProductController : ControllerBase
{
    private readonly IProductPricePredictionService 
      _productPricePredictionService;

    public ProductController(IProductPricePredictionService 
        IProductPricePredictionService productPricePredictionService)
    {
        _productPricePredictionService = productPricePredictionService;
    }

    //Action methods go here
}

Now, you'll create an HTTP POST endpoint that will be used to predict the price of a product if product code and region code is given to it as parameters. The following code snippet shows how you can create an action method that accepts product code and region code as parameters and calls the PredictPrice method of the ProductPricePredictionService class to get the predicted price of the product.

 
[HttpPost("predict")]
public async Task<ActionResult<double>> Predict([FromBody] 
    ProductPriceInput productPriceInput)
{
    if (productPriceInput == null || 
        string.IsNullOrWhiteSpace (productPriceInput.Product_Code) || 
        string.IsNullOrWhiteSpace (productPriceInput.Region_Code))
    {
        return BadRequest("Product_Code and Region_Code are required");
    }
    
    var price = await _productPricePredictionService.PredictPrice (
        productPriceInput.Product_Code, 
        productPriceInput.Region_Code);
        
    return Ok(price);
}
The complete source code of the ProductController class is given in Listing 6.

Listing 6: The ProductController class
[ApiController]
[Route("api/[controller]")]
public class ProductController : ControllerBase
{
    private readonly IProductPricePredictionService 
        _productPricePredictionService;

    public ProductController(IProductPricePredictionService 
        productPricePredictionService)
    {
        _productPricePredictionService = productPricePredictionService;
    }

    [HttpPost("predict")]
    public async Task<ActionResult<double>> Predict([FromBody] 
        ProductPriceInput productPriceInput)
    {
        if (productPriceInput == null || 
            string.IsNullOrWhiteSpace(productPriceInput.Product_Code) ||
            string.IsNullOrWhiteSpace(productPriceInput.Region_Code))
            return BadRequest("Product_Code and Region_Code required");

        var price = await _productPricePredictionService.PredictPrice(
            productPriceInput.Product_Code, productPriceInput.Region_Code);
        return Ok(price);
    }
}

The sequence diagram of the complete flow is given in Figure 11.

Figure 11: Sequence diagram of the complete flow
Figure 11: Sequence diagram of the complete flow
Create the User Interface in Blazor
In this section, you'll take advantage of Blazor to create the user interface that will consume the API methods you created earlier. Blazor, an open-source framework from Microsoft, empowers developers to create interactive web applications using C# and .NET. Blazor employs WebAssembly, a web standard that permits browser-based code execution from languages other than JavaScript, creating a fully featured, high-performance client-side development environment using C#. Blazor provides a modern, efficient, and versatile approach to web application development. The complete source code of the user interface is given in Listing 7.

Listing 7: The Product.razor page
@page "/products"
@inject HttpClient Http

<h3>Product List</h3>

@if (products == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <table class="table">
        <thead>
            <tr>
                <th>Id</th>
                <th>Code</th>
                <th>Name</th>
                <th>Price</th>
                <th>Stock</th>
                <th>Region</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var prod in products)
            {
                <tr>
                    <td>@prod.Id</td>
                    <td>@prod.Code</td>
                    <td>@prod.Name</td>
                    <td>@prod.Price</td>
                    <td>@prod.Stock</td>
                    <td>@prod.RegionCode</td>
                </tr>
            }
        </tbody>
    </table>
}

<h4>Predict Price</h4>
<div>
    <label>Product Code: </label>
    <input @bind="input.Code" style="margin-right:2em;" />

    <label>Region Code: </label>
    <input @bind="input.RegionCode" style="margin-right:2em;" />

    <button class="btn btn-primary" 
        @onclick="PredictPriceAsync">Predict</button>

    @if (predictedPrice != null)
    {
        <div style="margin-top:1em;">
            <strong>Predicted Price: </strong>@predictedPrice
        </div>
    }
</div>

@code {
    private readonly string apiBase = "https://localhost:5001";
    List<Product> products;
    PredictInput input = new();
    string predictedPrice;

    protected override async Task OnInitializedAsync()
    {
        products = await Http.GetFromJsonAsync<List<Product>>(
            $"{apiBase}/api/products");
    }

    async Task PredictPriceAsync()
    {
        var response = await Http.PostAsJsonAsync(
            $"{apiBase}/api/products/predict", input);
        if (response.IsSuccessStatusCode)
            predictedPrice = await response.Content.ReadAsStringAsync();
        else
            predictedPrice = "Error";
    }

    public class Product
    {
        public int Id { get; set; }
        public string Code { get; set; }
        public string Name { get; set; }
        public decimal Price { get; set; }
        public int Stock { get; set; }
        public string RegionCode { get; set; }
    }

    public class PredictInput
    {
        public string Code { get; set; }
        public string RegionCode { get; set; }
    }
}
Create a New Blazor Web Assembly Project in .NET 10 and Visual Studio 2022
You can create a project in Visual Studio 2022 in several ways, such as from the Visual Studio 2022 Developer Command Prompt or by launching the Visual Studio 2022 IDE. When you launch Visual Studio 2022, you'll see the Start window. You can choose “Continue without code” to launch the main screen of the Visual Studio 2022 IDE.

Now that you know the basics, let's start setting up the project. To create a new ASP.NET Core 8 Project in Visual Studio 2022:

Start the Visual Studio 2022 IDE.
In the Create a new project window, select Blazor Web App and click Next to move on.
Specify the project name as Product_Price_UI and the path where it should be created in the Configure your new project window.
If you want the solution file and project to be created in the same directory, you can optionally check the Place solution and project in the same directory checkbox. Click Next to move on.
In the next screen, specify the target framework and authentication type as well. Ensure that the Configure for HTTPS, and Do not use top-level statements checkboxes are unchecked because you won't use any of these in this example.
Next, specify the Interactive render mode and Interactivity location.
You should ensure that the Include sample pages checkbox is checked if you would like to have sample pages added to your project
Click Create to complete the process.
Implementing a Sales Forecast API Using ML.NET and ASP.NET Core
In this section, you'll implement an API that forecasts sales data of products based on historical data (i.e., sales data for the last ten years).

Create the Models
In this implementation, you'll use the following model classes:

ProductSales: Contains the sales value of a product
ProductSalesRecord: Stores forecasted sales data
SalesForecastInput: Stores input data
SalesForecastOutput: Stores sales forecast output data
SalesForecast: Contains a float array used to calculate sales forecast data.
The following piece of code shows how these model classes should look:

 
namespace SalesForecastAPI.Models
{
    public class ProductSales
    {
        public float Sales { get; set; }
    }
}
namespace SalesForecastAPI.Models
{
    public class ProductSalesRecord
    {
        public string Product_Code { get; set; }
        public int Year { get; set; }
        public int Month { get; set; }
        public int Sales { get; set; }
    }
}
namespace SalesForecastAPI.Models
{
    public class SalesForecastInput
    {
        public string Product_Code { get; set; }
        public int Month { get; set; }
    }
}
namespace SalesForecastAPI.Models
{
    public class SalesForecastOutput
    {
        public int Month { get; set; }
        public int Actual { get; set; }
        public int Forecast { get; set; }
    }
}
namespace SalesForecastAPI.Models
{
    public class SalesForecast
    {
        public float[] ForecastedSales { get; set; }
    }
}
Create the SalesTimeSeriesForecastService Class
Now, create a new interface named ISalesTimeSeriesForecastService in a file named ISalesTimeSeriesForecastService.cs and replace the default generated code with the following piece of code.

 
using SalesForecastAPI.Models;

namespace SalesForecastAPI
{
    public interface ISalesTimeSeriesForecastService
    {
        Task<List<SalesForecastOutput>> GetSalesForcastData(
            SalesForecastInput salesForecastInput);
    }
}

Next, create a new class named SalesTimeSeriesForecastService that implements the ISalesTimeSeriesForecastService interface in a file having an identical name with a .cs extension. In this class, you'll generate sales data that will be used to forecast sales of products. Apart from generating sales data, this class will also contain the necessary logic for generating forecasted sales data.

For the sake of simplicity, you'll generate a list of records instead of reading them from the database. The following piece of code shows how you can generate sales data for ten years for five products.

 
private List <ProductSalesRecord> 
GenerateSalesData()
{
    List <ProductSalesRecord> salesData = new List <ProductSalesRecord> ();
    string[] productCodes = {
        "P0001",
        "P0002",
        "P0003",
        "P0004",
        "P0005"
    };
    Random rand = new Random();

    for (int year = 2016; year <= 2025; year++)
    {
        for (int month = 1; month <= 12; month++)
        {
            foreach(var code in productCodes)
            {
                float baseSales = 110 f + 13 f * Array.IndexOf(
                    productCodes, code);
                float season = 18 f * (float) Math.Sin(
                    2 * Math.PI * month / 12);
                float trend = (year - 2016) * 2.5 f;
                float noise = (float)(rand.NextDouble() * 6 - 3);
                float sales = (float)(Math.Round(
                    baseSales + season + trend + noise));

                salesData.Add(new ProductSalesRecord
                {
                    Product_Code = code,
                    Year = year,
                    Month = month,
                    Sales = sales
                });
            }
        }
    }

    return salesData;
}

The GenerateSalesForecast method given below generates sales data using a time series prediction engine. Note how the CreateTimeSeriesEngine method has been called.

 
private float[]
GenerateSalesForecast(string productCode,int forecastHorizon,
    List<ProductSalesRecord> salesRecords)
{
    var series = salesRecords
        .Where(r => r.Product_Code == productCode)
        .OrderBy(r => r.Year * 100 + r.Month)
        .Select(r => new ProductSales { Sales = (float)r.Sales })
        .ToList();

    int seriesLength = series.Count;
    int windowSize = Math.Max(2, Math.Min(12, seriesLength / 20));

    if (seriesLength <= 2 * windowSize)
        return null;

    var dataView = _mlContext.Data.LoadFromEnumerable(series);

    var pipeline = _mlContext.Forecasting.ForecastBySsa(
        outputColumnName: nameof(SalesForecast.ForecastedSales),
        inputColumnName: nameof(ProductSales.Sales),
        windowSize: windowSize,
        seriesLength: seriesLength,
        trainSize: seriesLength,
        horizon: forecastHorizon
    );

    var model = pipeline.Fit(dataView);

    var engine = model.CreateTimeSeriesEngine<ProductSales, 
        SalesForecast>(_mlContext);

    return engine.Predict().ForecastedSales;
}
In the GetSalesForecastData method, the GenerateSalesForecastData method is called. This method returns a list of float values corresponding to the sales of products for one or more months.

 
var productSalesRecords = GenerateSalesData();
string prodCode = salesForecastInput.Product_Code;
int userMonth = int.Parse(salesForecastInput.Month.ToString());
int latestYear = productSalesRecords.Max(r => r.Year);

var yearData = productSalesRecords.Where(r => 
        r.Product_Code == prodCode && 
        r.Year == latestYear)
    .OrderBy(r => r.Month)
    .ToList();

float[] forecasts = GenerateSalesForecast(
    prodCode, 12, productSalesRecords);
If you'd like to ensure that the actual and forecasted values closely match, generate sales data with a clear, consistent pattern (such as linear growth, or clear seasonality with limited noise). This ensures that the forecast data generated is almost identical to the actual data trend.

Next, this list is used to create a formatted list of sales forecast data using the SalesForecastOutput class. The complete source code for the SalesTimeSeriesForecastService class is given in Listing 8.

Listing 8: The SalesTimeSeriesForecastService class
using Microsoft.ML;
using Microsoft.ML.Transforms.TimeSeries;
using SalesForecastAPI.Models;

namespace SalesForecastAPI
{
    public class SalesTimeSeriesForecastService : 
        ISalesTimeSeriesForecastService
    {
        private readonly MLContext _mlContext = new MLContext();

        private List<ProductSalesRecord> GenerateSalesData()
        {
            List<ProductSalesRecord> salesData = new List<ProductSalesRecord>();
            string[] productCodes = { 
                "P0001", "P0002", "P0003", "P0004", "P0005" };
            Random rand = new Random();

            for (int year = 2016; year <= 2025; year++)
            {
                for (int month = 1; month <= 12; month++)
                {
                    foreach (var code in productCodes)
                    {
                        float baseSales = 110f + 13f * Array.IndexOf(
                            productCodes, code);
                        float season = 18f * (float)Math.Sin(
                            2 * Math.PI * month / 12);
                        float trend = (year - 2016) * 2.5f;
                        float noise = (float)(rand.NextDouble() * 6 - 3);
                        int sales = Convert.ToInt32(Math.Round(
                            baseSales + season + trend + noise));

                        salesData.Add(new ProductSalesRecord
                        {
                            Product_Code = code,
                            Year = year,
                            Month = month,
                            Sales = sales
                        });
                    }
                }
            }
            return salesData;
        }

        private float[] Forecast(
            string productCode, 
            int forecastHorizon, 
            List<ProductSalesRecord> salesRecords)
        {
            var series = salesRecords
                .Where(r => r.Product_Code == productCode)
                .OrderBy(r => r.Year * 100 + r.Month)
                .Select(r => new ProductSales { Sales = (float)r.Sales })
                .ToList();

            int seriesLength = series.Count;
            int windowSize = Math.Max(2, Math.Min(12, seriesLength / 20));

            if (seriesLength <= 2 * windowSize)
                return null;

            var dataView = _mlContext.Data.LoadFromEnumerable(series);
            var pipeline = _mlContext.Forecasting.ForecastBySsa(
                outputColumnName: "ForecastedSales",
                inputColumnName: nameof(ProductSales.Sales),
                windowSize: windowSize,
                seriesLength: seriesLength,
                trainSize: seriesLength,
                horizon: forecastHorizon);

            var model = pipeline.Fit(dataView);
            var engine = model.CreateTimeSeriesEngine<ProductSales, 
                SalesForecastOutput>(_mlContext);
            return engine.Predict().ForecastedSales;
        }

        public List<SalesForecast> GetSalesForcastData(
            SalesForecastInput salesForecastInput)
        {
            var productSalesRecords = GenerateSalesData();
            string prodCode = salesForecastInput.Product_Code;
            int userMonth = int.Parse(salesForecastInput.Month.ToString());
            int latestYear = productSalesRecords.Max(r => r.Year);

            var yearData = productSalesRecords.Where(r => 
                    r.Product_Code == prodCode && 
                    r.Year == latestYear)
                .OrderBy(r => r.Month)
                .ToList();

            float[] forecasts = Forecast(prodCode, 12, productSalesRecords);
            List<SalesForecast> salesForecasts = new List<SalesForecast>();

            if (userMonth == 0)
            {
                for (int i = 0; i < 12; i++)
                {
                    string actual = yearData.Count > i ? 
                        yearData[i].Sales.ToString() : "-";
                    string forecast = (forecasts != null && 
                        i < forecasts.Length) ? 
                        Math.Round(forecasts[i]).ToString() : "-";

                    Console.WriteLine($"{i + 1,5} {actual,10} {forecast,12}");

                    SalesForecast salesForecast = new SalesForecast();
                    salesForecast.Actual = yearData.Count > i ? 
                        yearData[i].Sales : 0;
                    salesForecast.Forecast = (forecasts != null && 
                        i < forecasts.Length) ? forecasts[i] : 0;
                    salesForecast.Forecast = Math.Round(
                        salesForecast.Forecast, 2);
                    salesForecast.Month = i + 1;
                    salesForecasts.Add(salesForecast);
                }
            }
            else
            {
                int idx = userMonth - 1;
                string actual = yearData.Count > idx ? 
                    yearData[idx].Sales.ToString() : "-";
                string forecast = (forecasts != null && 
                    idx < forecasts.Length) ? 
                    Math.Round(forecasts[idx]).ToString() : "-";

                Console.WriteLine($"{userMonth,5} {actual,10} {forecast,12}");

                SalesForecast salesForecast = new SalesForecast();
                salesForecast.Actual = (forecasts != null && 
                    idx < forecasts.Length) ? 
                    forecasts[idx] : 0;
                salesForecast.Forecast = (forecasts != null && 
                    idx < forecasts.Length) ? 
                    forecasts[idx] : 0;
                salesForecast.Forecast = Math.Round(
                    salesForecast.Forecast, 2);
                salesForecast.Month = userMonth;
                salesForecasts.Add(salesForecast);
            }

            return salesForecasts;
        }
    }
}
The complete source code of the GetSalesForecastData method is given in Listing 9.

Listing 9: The GetSalesForcastData method
public Task<List<SalesForecastOutput>> GetSalesForcastData(
    SalesForecastInput salesForecastInput)
{
    var productSalesRecords = GenerateSalesData();
    string prodCode = salesForecastInput.Product_Code;
    int userMonth = int.Parse(salesForecastInput.Month.ToString());
    int latestYear = productSalesRecords.Max(r => r.Year);

    var yearData = productSalesRecords.Where(r => 
            r.Product_Code == prodCode && 
            r.Year == latestYear)
        .OrderBy(r => r.Month)
        .ToList();

    float[] forecasts = GenerateSalesForecast(
        prodCode, 
        12, 
        productSalesRecords);

    List<SalesForecastOutput> salesForecasts = 
        new List<SalesForecastOutput>();

    if (userMonth == 0)
    {
        for (int i = 0; i < 12; i++)
        {
            string actual = yearData.Count > i ? 
                yearData[i].Sales.ToString() : "-";
            string forecast = (forecasts != null && i < forecasts.Length) ? 
                Math.Round(forecasts[i]).ToString() : "-";

            Console.WriteLine($"{i + 1,5} {actual,10} {forecast,12}");

            SalesForecastOutput salesForecastResult = 
                new SalesForecastOutput();
            salesForecastResult.Actual = (int)Math.Ceiling(
                yearData.Count > i ? yearData[i].Sales : 0);
            salesForecastResult.Forecast = (int)Math.Ceiling(
                (forecasts != null && i < forecasts.Length) ? 
                forecasts[i] : 0);
            salesForecastResult.Month = i + 1;
            salesForecasts.Add(salesForecastResult);
        }
    }
    else
    {
        int idx = userMonth - 1;
        string actual = yearData.Count > idx ? 
            yearData[idx].Sales.ToString() : "-";
        string forecast = (forecasts != null && idx < forecasts.Length) ? 
            Math.Round(forecasts[idx]).ToString() : "-";

        Console.WriteLine($"{userMonth,5} {actual,10} {forecast,12}");

        SalesForecastOutput salesForecast = new SalesForecastOutput();
        salesForecast.Actual = (int)Math.Ceiling(
            (forecasts != null && idx < forecasts.Length) ? 
            forecasts[idx] : 0);
        salesForecast.Forecast = (int)Math.Ceiling(
            (forecasts != null && idx < forecasts.Length) ? 
            forecasts[idx] : 0);
        salesForecast.Month = userMonth;
        salesForecasts.Add(salesForecast);
    }

    return Task.FromResult(salesForecasts);
}
Register the Services in the Program.cs File
The following code snippet shows the Program.cs file. Note how the SalesTimeSeriesForecastService class has been registered with the request processing pipeline.

 
var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddControllers();
builder.Services.AddScoped<ISalesTimeSeriesForecastService, ,
    SalesTimeSeriesForecastService>();
builder.Services.AddOpenApi();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.MapOpenApi();
}
app.UseAuthorization();
app.MapControllers();
app.Run();

Implementing the SalesForecastController Class
The SalesForecast controller provides the API action methods. In this example, there is only one action method. In this class, you create an instance of type ISalesForecastService using dependency injection in the constructor, as shown in the code snippet given below:

 
public class SalesForecastController: ControllerBase
{
    private readonly ISalesTimeSeriesForecastService 
        _salesTimeSeriesForecastService;

    public SalesForecastController(ISalesTimeSeriesForecastService 
        salesTimeSeriesForecast)
    {
        _salesTimeSeriesForecastService = salesTimeSeriesForecast;
    }
}

This instance is used in the action method of the SalesForecastController class to retrieve the sales forecasted data of one or more products, as shown below.

 
[HttpGet]
public async Task<List<SalesForecast>> Get(SalesForecastInput salesForecastInput)
{
    var salesForecasts = await _salesTimeSeriesForecastService
        .GetSalesForcastData(salesForecastInput);
    return salesForecasts;
}

The complete source code of the SalesForecastController class is given in Listing 10. That's all you have to do to implement the API. Figure 12 shows the output looks when you execute the HttpGet action method of this class in Postman.

Listing 10: The SalesForecastController class
using Microsoft.AspNetCore.Mvc;
using SalesForecastAPI.Models;

namespace SalesForecastAPI.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class SalesForecastController : ControllerBase
    {
        private readonly ISalesTimeSeriesForecastService 
            _salesTimeSeriesForecastService;

        public SalesForecastController(
            ISalesTimeSeriesForecastService salesTimeSeriesForecast)
        {
            _salesTimeSeriesForecastService = salesTimeSeriesForecast;
        }

        [HttpGet]
        public async Task<List<SalesForecast>> Get(
            SalesForecastInput salesForecastInput)
        {
            var salesForecasts = await _salesTimeSeriesForecastService
                .GetSalesForcastData(salesForecastInput);
            return salesForecasts;
        }
    }
}
Figure 12: The sales forecast data is generated and shown in Postman.
Figure 12: The sales forecast data is generated and shown in Postman.
Recommend the Best Product Based on the Generated Sales Forecast Data
Finally, you can modify the API to recommend the best product based on the historical sales data. To achieve this, you should convert the product sales forecasting source code such that the API can recommend the best product to buy based on the historical sales data and ratings. You can add a new column called Rating, which can be the numbers from 1 to 5 with 1 the lowest and 5 the highest. Products that have higher sales usually have higher ratings. The following code snippet shows the change you need in the ProductSalesRecord class.

 
public class ProductSalesRecord
{
    public string Product_Code
    {
        get; set;
    }

    public int Year
    {
        get; set;
    }

    public int Month
    {
        get; set;
    }

    public float Sales
    {
        get; set;
    }

    public int Rating
    {
        get; set;
    }
}
When generating product sales data, the application should include the product ratings (integer values between 1 and 5), correlated with the sales values, i.e., the total number of products sold. Your application should then recommend the best product to buy using a weighted score that's calculated using the sales and rating values of a product. Your application should then apply a data-driven, multi-metric approach to recommend the right product based on the factors mentioned earlier.

Conclusion
