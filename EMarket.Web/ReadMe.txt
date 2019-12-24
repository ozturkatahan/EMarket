https://colorlib.com/preview/theme/wines/
https://themeforest.net/item/marten-pet-food-ecommerce-bootstrap4-template/22441520
https://colorlib.com/preview/theme/littlecloset/
https://colorlib.com/preview/theme/winter/
https://colorlib.com/preview/theme/onetech/
https://colorlib.com/preview/theme/shoppers/index.html
https://colorlib.com/preview/theme/shop/#women
https://colorlib.com/etc/fashe/index.html

https://themehunt.com/item/1527339-ezone-free-multipurpose-ecommerce-bootstrap4-template

EMarket.Web olarak ASP core 2.2 se�ilerek mvc individual se�ilir.
class lib EMarket.ApplicationCore olarak a��l�r class� sil .Net Core 2.2'ye �ek
class lib EMarket.Infrastructure olarak a��l�r class� sil .Net Core 2.2ye �ek

Referanslar verilir Bunu Web yapar di�er ikisine referans verir(Dependenciese sa� t�klla)
Infrastructure 'dan ApplicationCore referans verir

ApplicationCore i�erisine Interfaces,Services ve entites klas�rleri a��l�r
Infrastructure i�erisine data ve migrations diye klas�rleri a��l�r

ApplicationCore-Entities AppUser class� ac�l�r.
public class ApplicationUser : IdentityUser'den miras al�r�z ve Identityuser'� kullanmak ve bunun i�in 
install-package microsoft.aspnetcore.identity.entityframeworkcore -version 2.2.0 kurulumu yap�l�r.

EMarket.Web Data-->ApplicationDbContext class� kopyalan�r
ve Infrastructure.dataya ApplicationDbContext class� ac�larak buraya yap�st�r�l�r
ve IdentityDbContext kullanmak i�in 
infrastructure'a install-package microsoft.entityframeworkcore.sqlserver -version 2.2.0 kurulumu yap�l�r.
Bu class public class ApplicationDbContext : IdentityDbContext<ApplicationUser> haline getirilir.

EMarket.Web i�erisinde Data klas�r� silinir.

startupa <ApplicationDbContext> ctrl+.+enter namespace'den data silinir.
services.AddDefaultIdentity<IdentityUser>() yerine
services.AddDefaultIdentity<ApplicationUser>() bu hale gelir.

appsettings.json'a gel.
    "DefaultConnection": "Server=.;Database=EMarketDb; uid=sa pwd=123" g�ncelle�tirilir.

    EMarket.Infrastructure se�iliyken
    PM> add-migration Identity
    sonra update-database

    startup daha sonra ConfigureServices i�i services.AddIdentity<ApplicationUser, IdentityRole>() haline getirilir.

    _ViewImports'da @using EMarket.ApplicationCore.Entities eklenir ve _LoginPartial'da
    @inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager haline getirilir.
EMarket.ApplicationCore Entities'e BaseEntity ve Category Class'� ac�l�r

Infrastructure
ApplicationDbContext'e
public DbSet<Category> Categories { get; set; } eklenir.
 daha sonra
 override OnModelCreating olu�turulur.

 EMarket.Infrastructure->Data->Config klas�r� a� CategoryConfiguration class� a�

 public class CategoryConfiguration : IEntityTypeConfiguration<Category> haline gelir ve Configura olarak implement edilir.

 Infrastructure'da
 add-migration CategoryEntity

 An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
hatas� al�nd��� i�in migrations klas�r� silinir veri taban� ssms'den silinir.

clean solution ve yeniden
add-migration Identity denir 0'dan add-migrations olmus olur.
update-database

ApplicationCore->Interface i�erisine new item-interface IRepository.cs a��l�r.��erisine <T> tipler girilir.

infrastructure->Data->add class EfRepository class'� ac�l�r ve
bu hale getirilir.
public class EfRepository<T> : IRepository<T> where T : BaseEntity

 IRepository<T> implement edilecek.

 EfRepository class i�erisi doldurulur.

 SERV�SLER'DE APPLICATIONCORE'da yer al�r.

 EMarket.ApplicationCore->interfaces-> ICategoryService ad�nda bir interface ac�l�r. ��i doldurulur.Bunu uyguluyoruz.
 Servicese CategoryService ad�nda class a��l�r.
 public class CategoryService : ICategoryService haline getirilir ve implement edilir.
 Veri taban�na elle iki kategori girdik.

 Web->startup->
             services.AddScoped(typeof(IRepository<>), typeof(EfRepository<>));
            services.AddScoped<ICategoryService, CategoryService>();

 EMarket.Web->home controller indexe service'leri verdik.
 gotoview->model olustur.


 --------16.12.2019--------
 infrastructure'da
 update-database -migration:0

 daha sonra startup.cs'de
 Configure 0i�erisine en tepeye; using ile scope i�erisinde gerekli servis al�n�r.
 using (var scope = app.ApplicationServices.GetRequiredService<IServiceScopeFactory>().CreateScope()) metodunu yoruma al�yoruz.

 Market.Infrastructure.Data'ya ApplicationDbContextSeed class'� a��l�r. i�ine kategoriler eklenir.

 EMarket.Web->Program.cs'ye CreateWebHostBuilder de�i�iklik yap�l�r 2 sat�ra b�l�n�r.aras�na using eklenir.

 EMarket.Infrastructure.Data-ApplicationDbContextSeed'e usermanager ve rolemanager eklenir.

 Program.cs'de  usermanager ve rolemanager tan�mlan�r.

 --------17.12.2019--------
 ApplicationDbContextSeed Seed'metodunun ad� de�i�tirilerek SeedProductsAndCategories olarak de�i�tirilir.
  SeedUserAsync 'nin yerine SeedUsersandRolesAsync yap�ld�.
  Program.cs'e git Seed ve SeedUserAsync'i de�i�tir.

  EMarket.ApplicationCore.Entities'e product class'� ac�l�r baseentity'den miras al�r.��erisi doldurulur.
  ApplicationDbContext.cs'e public DbSet<Product> Products { get; set; } eklenir

    EMarket.ApplicationCore.Entities'de category class�na 
  public List<Product> Products { get; set; } eklenir.

  EMarket.Infrastructure.Data.Config'e prodyctconfiguration ad�nda class ac�l�r.��erisi doldurulur.
  add-migration products
  update-database

  EMarket.ApplicationCore klas�r a� Constants ad�nda..
  class a� i�ine AuthorizationConstants bu isimle.
  i�ini doldur.

  ApplicationDbContextSeed 
  "admin" yerine AuthorizationConstants.Roles.ADMINISTRATOR gelmeli
  password yerine AuthorizationConstants.DEFAULT_PASSWORD

  applicationDbContextSeed'e 
  private static readonly Random rnd = new Random();
  public static List<Category> Categories(int count = 5, int productCountPerCategory = 99) 
  
  metodu yaz�l�r

  ve seed i�erisinde
  if (!context.Categories.Any()) i�eri�i de�i�tirilir.

  EMarket.Infrastructure'da
  bundan sonra update-database -migration:0
  update-database


  shop.html i�i kopyalan�r.2.bir layout ac�l�r.i�i silinerek hepsi yap��t�r�l�r.
  assets'lere yollar� verilir ~/ olarak
  daha sonra homecontroller�n indexine layout'u veririz.hangisini kullanaca��m�z�
  breadcrumbl� div'i k�s ve sil 287.sat�r
  categories sidebar hari� di�erleri silinir.

  EMarket.Web alt�na ViewModels klas�r� a��yoruz.
  ��erisine HomeIndexViewModel class� a��l�p i�erisi doldurulur.
  ViewImports'a @using EMarket.Web.ViewModels eklenir

  web'e Interfaces ve Services classlar� ac�l�r.


  ---EKS�K----

  --------18.12.2019---------

  ViewModel i�erisine CategoryViewModel class'� ac�l�r.
  -----EKS�K-----

----------19.12.2019---------
