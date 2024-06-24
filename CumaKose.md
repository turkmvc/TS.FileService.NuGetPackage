Hocam merhaba
ben dosya silme işlemini şu şekilde çözebildim
CleanArchitecture yapısında kodlarken
Program.cs de
Infrastructure katmanını tanıtırken
builder.Services.AddPresentation(builder.Configuration, builder.Environment);

şeklinde Environment gönderdim

Infrastructure Dependency Injection kısmında ise
public static IServiceCollection AddPersistance(
        this IServiceCollection services,
        IConfiguration configuration,
        IWebHostEnvironment webHostEnvironment)
        olarak aldım


        ayarlamalarını yaptım

        var myHostEnvironment = new MyHostEnvironment
        {
            WebRootPath = Path.Combine(Directory.GetCurrentDirectory(), "wwwroot"),
            WebRootFileProvider = new PhysicalFileProvider(Path.Combine(Directory.GetCurrentDirectory(), "wwwroot")),
            EnvironmentName = webHostEnvironment.EnvironmentName,
            ApplicationName = webHostEnvironment.ApplicationName,
            ContentRootPath = webHostEnvironment.ContentRootPath,
            ContentRootFileProvider = webHostEnvironment.ContentRootFileProvider
        };

        services.AddSingleton<IMyHostEnvironment>(myHostEnvironment);

        Singleton yaşam döngüsüne Interface ve Class olarak tanımladım
        Interface yi Domain katmanında Repositories klasöründe
        tanımladım

        public interface IMyHostEnvironment : IHostEnvironment
        {
            string WebRootPath { get; set; }

            IFileProvider WebRootFileProvider { get; set; }
        }


Infrastructure katmanında Class olarak


internal sealed class MyHostEnvironment : IMyHostEnvironment
{
    public string WebRootPath { get; set; } = default;

    public IFileProvider WebRootFileProvider { get; set; } = default;

    public string EnvironmentName { get; set; } = default;

    public string ApplicationName { get; set; } = default;

    public string ContentRootPath { get; set; } = default;

    public IFileProvider ContentRootFileProvider { get; set; } = default;
}

tanımladım
dosya silme işlemini Handler içerisinde


        internal sealed class DeleteByIdDepartmentCommandHandler(
            IDepartmentRepository departmentRepository,
            IUnitOfWork unitOfWork,
            IMyHostEnvironment hostEnvironment
        ) : IRequestHandler<DeleteByIdDepartmentCommand, Result<string>>

şeklinde interface mi Primary Constructure de çagırdım

handle içerisinde ise
 
        var fullPath = Path.Combine(hostEnvironment.WebRootPath, "departments", department.Image);

        if (File.Exists(fullPath))
        {
            File.Delete(fullPath);
        }
        bu şekilde dosyanın tam yolu path ve dosya olarak geliyor.
