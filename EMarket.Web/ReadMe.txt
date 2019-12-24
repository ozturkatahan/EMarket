https://colorlib.com/preview/theme/wines/
https://themeforest.net/item/marten-pet-food-ecommerce-bootstrap4-template/22441520
https://colorlib.com/preview/theme/littlecloset/
https://colorlib.com/preview/theme/winter/
https://colorlib.com/preview/theme/onetech/
https://colorlib.com/preview/theme/shoppers/index.html
https://colorlib.com/preview/theme/shop/#women
https://colorlib.com/etc/fashe/index.html

https://themehunt.com/item/1527339-ezone-free-multipurpose-ecommerce-bootstrap4-template

EMarket.Web olarak ASP core 2.2 seçilerek mvc individual seçilir.
class lib EMarket.ApplicationCore olarak açýlýr classý sil .Net Core 2.2'ye çek
class lib EMarket.Infrastructure olarak açýlýr classý sil .Net Core 2.2ye çek

Referanslar verilir Bunu Web yapar diðer ikisine referans verir(Dependenciese sað týklla)
Infrastructure 'dan ApplicationCore referans verir

ApplicationCore içerisine Interfaces,Services ve entites klasörleri açýlýr
Infrastructure içerisine data ve migrations diye klasörleri açýlýr

ApplicationCore-Entities AppUser classý acýlýr.
public class ApplicationUser : IdentityUser'den miras alýrýz ve Identityuser'ý kullanmak ve bunun için 
install-package microsoft.aspnetcore.identity.entityframeworkcore -version 2.2.0 kurulumu yapýlýr.

EMarket.Web Data-->ApplicationDbContext classý kopyalanýr
ve Infrastructure.dataya ApplicationDbContext classý acýlarak buraya yapýstýrýlýr
ve IdentityDbContext kullanmak için 
infrastructure'a install-package microsoft.entityframeworkcore.sqlserver -version 2.2.0 kurulumu yapýlýr.
Bu class public class ApplicationDbContext : IdentityDbContext<ApplicationUser> haline getirilir.

EMarket.Web içerisinde Data klasörü silinir.

startupa <ApplicationDbContext> ctrl+.+enter namespace'den data silinir.
services.AddDefaultIdentity<IdentityUser>() yerine
services.AddDefaultIdentity<ApplicationUser>() bu hale gelir.

appsettings.json'a gel.
    "DefaultConnection": "Server=.;Database=EMarketDb; uid=sa pwd=123" güncelleþtirilir.

    EMarket.Infrastructure seçiliyken
    PM> add-migration Identity
    sonra update-database

    startup daha sonra ConfigureServices içi services.AddIdentity<ApplicationUser, IdentityRole>() haline getirilir.

    _ViewImports'da @using EMarket.ApplicationCore.Entities eklenir ve _LoginPartial'da
    @inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager haline getirilir.
EMarket.ApplicationCore Entities'e BaseEntity ve Category Class'ý acýlýr

Infrastructure
ApplicationDbContext'e
public DbSet<Category> Categories { get; set; } eklenir.
 daha sonra
 override OnModelCreating oluþturulur.

 EMarket.Infrastructure->Data->Config klasörü aç CategoryConfiguration classý aç

 public class CategoryConfiguration : IEntityTypeConfiguration<Category> haline gelir ve Configura olarak implement edilir.

 Infrastructure'da
 add-migration CategoryEntity

 An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
hatasý alýndýðý için migrations klasörü silinir veri tabaný ssms'den silinir.

clean solution ve yeniden
add-migration Identity denir 0'dan add-migrations olmus olur.
update-database

ApplicationCore->Interface içerisine new item-interface IRepository.cs açýlýr.Ýçerisine <T> tipler girilir.

infrastructure->Data->add class EfRepository class'ý acýlýr ve
bu hale getirilir.
public class EfRepository<T> : IRepository<T> where T : BaseEntity

 IRepository<T> implement edilecek.

 EfRepository class içerisi doldurulur.

 SERVÝSLER'DE APPLICATIONCORE'da yer alýr.

 EMarket.ApplicationCore->interfaces-> ICategoryService adýnda bir interface acýlýr. Ýçi doldurulur.Bunu uyguluyoruz.
 Servicese CategoryService adýnda class açýlýr.
 public class CategoryService : ICategoryService haline getirilir ve implement edilir.
 Veri tabanýna elle iki kategori girdik.

 Web->startup->
             services.AddScoped(typeof(IRepository<>), typeof(EfRepository<>));
            services.AddScoped<ICategoryService, CategoryService>();

 EMarket.Web->home controller indexe service'leri verdik.
 gotoview->model olustur.


 --------16.12.2019--------
 infrastructure'da
 update-database -migration:0

 daha sonra startup.cs'de
 Configure 0içerisine en tepeye; using ile scope içerisinde gerekli servis alýnýr.
 using (var scope = app.ApplicationServices.GetRequiredService<IServiceScopeFactory>().CreateScope()) metodunu yoruma alýyoruz.

 Market.Infrastructure.Data'ya ApplicationDbContextSeed class'ý açýlýr. içine kategoriler eklenir.

 EMarket.Web->Program.cs'ye CreateWebHostBuilder deðiþiklik yapýlýr 2 satýra bölünür.arasýna using eklenir.

 EMarket.Infrastructure.Data-ApplicationDbContextSeed'e usermanager ve rolemanager eklenir.

 Program.cs'de  usermanager ve rolemanager tanýmlanýr.

 --------17.12.2019--------
 ApplicationDbContextSeed Seed'metodunun adý deðiþtirilerek SeedProductsAndCategories olarak deðiþtirilir.
  SeedUserAsync 'nin yerine SeedUsersandRolesAsync yapýldý.
  Program.cs'e git Seed ve SeedUserAsync'i deðiþtir.

  EMarket.ApplicationCore.Entities'e product class'ý acýlýr baseentity'den miras alýr.Ýçerisi doldurulur.
  ApplicationDbContext.cs'e public DbSet<Product> Products { get; set; } eklenir

    EMarket.ApplicationCore.Entities'de category classýna 
  public List<Product> Products { get; set; } eklenir.

  EMarket.Infrastructure.Data.Config'e prodyctconfiguration adýnda class acýlýr.Ýçerisi doldurulur.
  add-migration products
  update-database

  EMarket.ApplicationCore klasör aç Constants adýnda..
  class aç içine AuthorizationConstants bu isimle.
  içini doldur.

  ApplicationDbContextSeed 
  "admin" yerine AuthorizationConstants.Roles.ADMINISTRATOR gelmeli
  password yerine AuthorizationConstants.DEFAULT_PASSWORD

  applicationDbContextSeed'e 
  private static readonly Random rnd = new Random();
  public static List<Category> Categories(int count = 5, int productCountPerCategory = 99) 
  
  metodu yazýlýr

  ve seed içerisinde
  if (!context.Categories.Any()) içeriði deðiþtirilir.

  EMarket.Infrastructure'da
  bundan sonra update-database -migration:0
  update-database


  shop.html içi kopyalanýr.2.bir layout acýlýr.içi silinerek hepsi yapýþtýrýlýr.
  assets'lere yollarý verilir ~/ olarak
  daha sonra homecontrollerýn indexine layout'u veririz.hangisini kullanacaðýmýzý
  breadcrumblý div'i kýs ve sil 287.satýr
  categories sidebar hariç diðerleri silinir.

  EMarket.Web altýna ViewModels klasörü açýyoruz.
  Ýçerisine HomeIndexViewModel classý açýlýp içerisi doldurulur.
  ViewImports'a @using EMarket.Web.ViewModels eklenir

  web'e Interfaces ve Services classlarý acýlýr.


  ---EKSÝK----

  --------18.12.2019---------

  ViewModel içerisine CategoryViewModel class'ý acýlýr.
  -----EKSÝK-----

----------19.12.2019---------
