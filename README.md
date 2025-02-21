[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/0lo6gDOJ)
[![Open in Codespaces](https://classroom.github.com/assets/launch-codespace-2972f46106e565e64193e422d61a12cf1da4916b45550586e14ef0a7c637dd04.svg)](https://classroom.github.com/open-in-codespaces?assignment_repo_id=18257369)
# SESION DE LABORATORIO N° 03: Construyendo una aplicación Web utilizando Blazor Server

### Nombre: 

## OBJETIVOS
  * Desarrolla una aplicación web utilizando Blazor Server.

## REQUERIMIENTOS
  * Conocimientos: 
    - Bash o Powershell.
    - AWS CLI.
    - Net / Blazor
  * Hardware:
    - Virtualization activada en el BIOS.
    - CPU SLAT-capable feature.
    - Al menos 4GB de RAM.
  * Software:
    - Docker Desktop 
    - AWS CLI 2.x.x
    - Python 3.10 o superior
    - EB CLI
    - Dotnet 8
## CONSIDERACIONES INICIALES
  * Obtener y copiar los valores de acceso a nube (CLOUD ACCESS - AWS CLI) ubicados en el curso de LEARNER LAB de AWS Academy
    ```
    [default]
    aws_access_key_id=.....
    aws_secret_access_key=....
    aws_session_token=......
    ```
    1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador
    2. En el terminal, ejecutar el siguiente comando para crear la carpeta y archivos de conexion a AWS
    ```Powershell
    md $env:USERPROFILE\.aws
    New-Item -Path $env:USERPROFILE\.aws -Name credentials -ItemType File
    New-Item -Path $env:USERPROFILE\.aws -Name config -ItemType File
    ```
    3. En el terminal, ejecutar el siguiente comando para editar el archivo de credenciales.
    ```Powershell
    notepad $env:USERPROFILE\.aws\credentials
    ```
    > Pegar los valores previamente obtenidos CLOUD ACCESS - AWS CLI y guardar los cambios en el archivos
    4. En el terminal, ejecutar el siguiente comando para editar el archivo de configuracion.
    ```Powershell
    notepad $env:USERPROFILE\.aws\config
    ```
    > Pegar el siguiente contenido y guardar los cambios.
    ```
    [default]
    region=us-east-1
    ```
    5. Cerrar y volver a abrir el terminal de Powershell
  * Clonar el repositorio mediante git para tener los recursos necesarios en una ubicación que no sea del sistema.
  * Colocar su nombre en el archivo   
    
## DESARROLLO
1. Iniciar la aplicación Powershell o Windows Terminal en modo administrador.
2. En el terminal, ubicarse en un ruta que no sea del sistema y ejecutar los siguientes comandos.
```Bash
md src
cd src
dotnet new blazor -o Financiera.WebApp
dotnet add package Microsoft.EntityFrameworkCore --version 8.0.0
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 8.0.0
dotnet add package Microsoft.EntityFrameworkCore.Design --version 8.0.0
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 8.0.0
dotnet add package Microsoft.EntityFrameworkCore.Tools --version 8.0.0
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 8.0.0
dotnet add package Microsoft.AspNetCore.Components.QuickGrid --version 8.0.0
dotnet add package Microsoft.AspNetCore.Components.QuickGrid.EntityFrameworkAdapter --version 8.0.0
dotnet tool install -g dotnet-ef --version 8.0.0
dotnet tool install -g dotnet-aspnet-codegenerator --version 8.0.0
```

3. Abrir Visual Studio Code y elegir la carpeta del proyecto, dentro del proyecto Financiera.WebApp, crear la carpeta Modelos, y dentro de esta crear los siguientes archivos con lo siguientes contenidos:
> Cliente.cs
```CSharp
namespace Financiera.WebApp.Modelos;
/// <summary>
/// Entidad de Dominio que representa al Cliente
/// </summary>
public class Cliente
{
    /// <summary>
    /// Identificador único del cliente
    /// </summary>
    public int IdCliente { get; set; }
    /// <summary>
    /// Nombre completo del cliente
    /// </summary>
    public string NombreCliente { get; set; }
    
    /// <summary>
    /// Permite registrar a un nuevo cliente
    /// </summary>
    /// <param name="_nombre">Nombre completo del cliente</param>
    /// <returns>Instancia nueva de la clase Cliente</returns>
    public static Cliente Registrar(string _nombre)
    {
        return new Cliente(){
            NombreCliente = _nombre
        };
    }
    /// <summary>
    /// Permite modificar el nombre del cliente
    /// </summary>
    /// <param name="_nombre"></param>
    public void ModificarNombre(string _nombre)
    {
        NombreCliente = _nombre;
    }

}
```
> CuentaAhorro.cs
```CSharp
namespace Financiera.WebApp.Modelos;
/// <summary>
/// Clase de Dominio que representa una cuenta de ahorro
/// </summary>
public class CuentaAhorro
{
    /// <summary>
    /// 
    /// </summary>
    public int IdCuenta { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public string NumeroCuenta { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public virtual Cliente Propietario { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public int IdPropietario { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public decimal Tasa { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public decimal Saldo { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public DateTime FechaApertura { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public bool Estado { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public ICollection<MovimientoCuenta> Movimientos { get; private set; }
    
    /// <summary>
    /// Primer constructor
    /// </summary>
    public CuentaAhorro()
    {
        Movimientos = new List<MovimientoCuenta>();
    }
    /// <summary>
    /// 
    /// </summary>
    /// <param name="_numeroCuenta"></param>
    /// <param name="_propietario"></param>
    /// <param name="_tasa"></param>
    /// <returns></returns>
    public static CuentaAhorro Aperturar(string _numeroCuenta, Cliente _propietario, decimal _tasa)
    {
        return new CuentaAhorro()
        {
            NumeroCuenta = _numeroCuenta,
            Propietario = _propietario,
            IdPropietario = _propietario.IdCliente,
            Tasa = _tasa,
            Saldo = 0
        };
    }

    public const string ERROR_MONTO_MENOR_IGUAL_A_CERO = "El monto no puede ser menor o igual a 0";
    
    public void Depositar(decimal monto)
    {
        if (monto <= 0)
            throw new Exception (ERROR_MONTO_MENOR_IGUAL_A_CERO);
        Saldo += monto;
    }
    
    public void Retirar(decimal monto)
    {
        if (monto <= 0)
            throw new Exception (ERROR_MONTO_MENOR_IGUAL_A_CERO);
        Saldo -= monto;
    }
}
```
> MovimientoCuenta.cs
```CSharp
namespace Financiera.Dominio.Modelos;
/// <summary>
/// Clase de WebApp que representa el movimientos de la cuenta
/// </summary>
public class MovimientoCuenta
{
    /// <summary>
    /// 
    /// </summary>
    public int NumeroMovimiento { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public CuentaAhorro Cuenta { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public int IdCuenta { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public TipoMovimiento Tipo { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public int IdTipoMovimiento { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public DateTime FechaMovimiento { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public decimal MontoMovimiento { get; private set; }
    /// <summary>
    /// 
    /// </summary>
    public bool Estado { get; private set; }

    /// <summary>
    /// 
    /// </summary>
    /// <param name="_cuenta"></param>
    /// <param name="_tipo"></param>
    /// <param name="_monto"></param>
    /// <returns></returns>
    public static MovimientoCuenta Crear(CuentaAhorro _cuenta, TipoMovimiento _tipo, decimal _monto)
    {
        return new MovimientoCuenta() {
            Cuenta = _cuenta,
            IdCuenta = _cuenta.IdCuenta,
            Tipo = _tipo,
            IdTipoMovimiento = _tipo.IdTipo,
            FechaMovimiento = DateTime.Now,
            MontoMovimiento = _monto,
            Estado = true
        };
    }
    /// <summary>
    /// 
    /// </summary>
    public void Anular()
    {
        Estado = false;
    }
}
```
> TipoMovimiento.cs
```CSharp
namespace Financiera.WebApp.Modelos;
/// <summary>
/// Entidad de Dominio que representa un tipo de movimiento de cuenta de ahorro
/// </summary>
public class TipoMovimiento
{
    /// <summary>
    /// Representa el identificador del tipo de movimiento
    /// </summary>
    public int IdTipo { get; private set; }
    /// <summary>
    /// Representa la descripcion del tipo de movimiento
    /// </summary>
    public string DescripcionTipo { get; private set; }

    /// <summary>
    /// 
    /// </summary>
    /// <param name="_descripcion"></param>
    /// <returns></returns>
    public static TipoMovimiento Registrar(string _descripcion)
    {
        return new TipoMovimiento(){
            DescripcionTipo = _descripcion
        };
    }
    /// <summary>
    /// 
    /// </summary>
    /// <param name="_descripcion"></param>
    /// <returns></returns>
    public static TipoMovimiento Crear(int _id, string _descripcion)
    {
        return new TipoMovimiento(){
            IdTipo = _id,
            DescripcionTipo = _descripcion
        };
    }
}
```

![TipoMovimiento](https://github.com/UPT-FAING-EPIS/lab-2024-rec-si982-u2-02-MarceloCuadros/blob/main/tipomovimiento.png)

4. En Visual Studio Code, dentro del proyecto Financiera.WebApp, crear la carpeta Mapeos, y dentro de esta crear los siguientes archivos con lo siguientes contenidos:
> ClienteConfiguracion.cs
```CSharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using Financiera.WebApp.Modelos;
namespace Financiera.WebApp.Mapeos;
public class ClienteConfiguracion : IEntityTypeConfiguration<Cliente>
{
    public void Configure(EntityTypeBuilder<Cliente> builder)
    {
        builder.ToTable("CLIENTES");
        builder.HasKey(c => c.IdCliente);
        builder.Property(c => c.IdCliente).HasColumnName("ID_CLIENTE").HasComment("Identificador unico del cliente");
        builder.Property(c => c.NombreCliente).HasColumnName("NOM_CLIENTE").HasComment("Nombre del cliente").HasMaxLength(200).IsRequired();
    }
}
```
> CuentaAhorroConfiguracion.cs
```CSharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using Financiera.WebApp.Modelos;
namespace Financiera.WebApp.Mapeos;
public class CuentaAhorroConfiguracion : IEntityTypeConfiguration<CuentaAhorro>
{
    public void Configure(EntityTypeBuilder<CuentaAhorro> builder)
    {
        builder.ToTable("CUENTAS_AHORRO");
        builder.HasKey(c => c.IdCuenta);
        builder.Property(c => c.IdCuenta).HasColumnName("ID_CUENTA").IsRequired();
        builder.Property(c => c.NumeroCuenta).HasColumnName("NUM_CUENTA").HasMaxLength(10).IsRequired();
        builder.Property(c => c.IdPropietario).HasColumnName("ID_CLIENTE").IsRequired();
        builder.HasOne(c => c.Propietario).WithMany().HasForeignKey(f => f.IdPropietario);
    }
}
```
> MovimientoCuentaConfiguracion.cs
```CSharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using Financiera.WebApp.Modelos;
namespace Financiera.WebApp.Mapeos;
public class MovimientoCuentaConfiguracion : IEntityTypeConfiguration<MovimientoCuenta>
{
    public void Configure(EntityTypeBuilder<MovimientoCuenta> builder)
    {
        builder.ToTable("MOVIMIENTOS_CUENTA");
        builder.HasKey(c => c.NumeroMovimiento);
        builder.Property(c => c.NumeroMovimiento).HasColumnName("NUM_MOVIMIENTO").IsRequired();
        builder.Property(c => c.IdCuenta).HasColumnName("ID_CUENTA").IsRequired();
        builder.Property(c => c.IdTipoMovimiento).HasColumnName("ID_TIPO");
        builder.Property(c => c.MontoMovimiento).HasColumnName("MON_MOVIMIENTO").IsRequired();
        builder.HasOne(c => c.Cuenta).WithMany().HasForeignKey(f => f.IdCuenta);
        builder.HasOne(c => c.Tipo).WithMany().HasForeignKey(f => f.IdTipoMovimiento);
    }
}
```
> TipoMovimientoConfiguracion.cs
```CSharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using Financiera.WebApp.Modelos;
namespace Financiera.WebApp.Mapeos;
public class TipoMovimientoConfiguracion : IEntityTypeConfiguration<TipoMovimiento>
{
    public void Configure(EntityTypeBuilder<TipoMovimiento> builder)
    {
        builder.ToTable("TIPOS_MOVIMIENTO");
        builder.HasKey(c => c.IdTipo);
        builder.Property(c => c.IdTipo).HasColumnName("ID_TIPO").UseMySqlIdentityColumn();
        builder.Property(c => c.DescripcionTipo).HasColumnName("DES_TIPO").HasMaxLength(100).IsRequired();
    }
}
```
![TipoMovimientoC](https://github.com/UPT-FAING-EPIS/lab-2024-rec-si982-u2-02-MarceloCuadros/blob/main/tipomovimientoc.png)


7. En Visual Studio Code, dentro del proyecto Financiera.WebApp, en la raiz crear el siguiente archivo y contenido:
> FinancieraContexto.cs
```CSharp
using Financiera.WebApp.Modelos;
using Microsoft.EntityFrameworkCore;
namespace Financiera.WebApp;
/// <summary>
/// Clase que contiene las Modelos y configuraciones de persistencia
/// del contexto Financiera
/// </summary>
public class FinancieraContexto : DbContext 
{
    // INICIO: Comentar o eliminar esta seccion luego de la migracion
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString));
    }
    // FIN
    /// <summary>
    /// Conjunto de datos cliente
    /// </summary>
    public DbSet<Cliente> Clientes { get; set; }
    /// <summary>
    /// Conjunto de datos TiposMovimiento
    /// </summary>
    public DbSet<TipoMovimiento> TiposMovimiento { get; set; }
    /// <summary>
    /// Conjunto de datos Cuentas de Ahorro
    /// </summary>
    /// <value></value>
    public DbSet<CuentaAhorro> CuentasAhorro { get; set; }
    /// <summary>
    /// Conjunto de Datos de Movimientos de Cuentas
    /// </summary>
    public DbSet<MovimientoCuenta> MovimientosCuenta { get; set; }
    /// <summary>
    /// Configuración de cada entidad hacia la base de datos
    /// </summary>
    /// <param name="modelBuilder">Constructor del modelo</param>
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.ApplyConfiguration(new Mapeos.ClienteConfiguracion());
        modelBuilder.ApplyConfiguration(new Mapeos.TipoMovimientoConfiguracion());
        modelBuilder.ApplyConfiguration(new Mapeos.CuentaAhorroConfiguracion());
        modelBuilder.ApplyConfiguration(new Mapeos.MovimientoCuentaConfiguracion());
    } 
}
```

8. En el terminal, ejecutar el siguiente comando para iniciar una base de datos MariaDB:
```Bash
md site && cs site
eb init Financiera.WebApp -r us-east-1 -p 64bit-amazon-linux-2023-v3.3.0-running-.net-8
eb create dev-env -s -sr LabRole -ip LabInstanceProfile -db
```
> Nota: para la base de datos se utilizar las credenciales usuario: sqladmin y password: upt.2025

9. En el terminal, verificar las instancias de base de datos creadas.
```Bash
aws rds describe-db-instances
```

10. En el terminal, ejecutar el siguiente comando para añadir el puerto de entrada a la base de datos (1521) el cual servira para la comunicación, reemplazar el valor de group-id por el valor de VpcSecurityGroupId obtenido en el paso anterior
```Bash
aws ec2 authorize-security-group-ingress --group-id sg-XXXXXXXXXXXXXX --protocol tcp --port 1521 --cidr 0.0.0.0/0
```

11. En Visual Studio Code, editar el archivo appsetting.json, que se encuentra en el proyecto Financiera.WebApp, y adicionar lo siguiente despues de la apertura de la primera llave.
```JSON
  "ConnectionStrings": {
    "FinancieraBD": "Server=XXXXXXXXXXXXXXXX; User ID=sqlamin; Password=upt.2025; Database=ebdb"
  },
```
> Nota: Reemplazar las XXXXXXXXXX con el nombre del servidor de base de datos generado previamente.
12. En Visual Studio Code, en el proyecto Financiera.WebApp modificar el archivo program.cs, al inicio del archivo agregar
```C#
using Financiera.WebApp;
using Microsoft.EntityFrameworkCore;
```
13. En Visual Studio Code, en el proyecto Financiera.WebApp modificar el archivo program.cs, debajo de la linea que indica `// Add services to the container`.
```C#
string connectionString = builder.Configuration.GetConnectionString("FinancieraBD")??"";
builder.Services.AddDbContext<FinancieraContexto>(
    opt => opt.UseMySql(connectionString, ServerVersion.AutoDetect(connectionString))
);
builder.Services.AddQuickGridEntityFrameworkAdapter();
```

14. En el terminal, esperar un minuto que inicie correctamente el contenedor y ejecutar el siguiente comando para efectuar la migración:
```Bash
cd Financiera.WebApp
dotnet ef migrations add CrearFinancieraBD
dotnet ef database update
```
15. En el terminal, proceder a generar la interfaz del cliente con el siguiente comando:
```Bash
cd Financiera.WebApp
dotnet aspnet-codegenerator blazor CRUD -m Cliente -dc FinancieraContexto
cd ..
```

16. En Visual Studio Code, en el proyecto Financiera.WebApp en la ruta Components\Layout modificar el archivo NavMenu.razor
> dice
```Razor
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="weather">
                <span class="bi bi-list-nested-nav-menu" aria-hidden="true"></span> Weather
            </NavLink>
        </div>
```
> debe decir
```Razor
        <div class="nav-item px-3">
            <NavLink class="nav-link" href="clientes">
                <span class="bi bi-list-nested-nav-menu" aria-hidden="true"></span> Custormers
            </NavLink>
        </div>
```

![NavMenu](https://github.com/UPT-FAING-EPIS/lab-2024-rec-si982-u2-02-MarceloCuadros/blob/main/navmenu.png)

17. En el terminal, para revisar la aplicación utilizar el siguiente comando:
```
eb open
```
![App](https://github.com/UPT-FAING-EPIS/lab-2024-rec-si982-u2-02-MarceloCuadros/blob/main/app.png)
---
## Actividades Encargadas
1. Construir el archivo terraform (archivo main.tf) para aprovisionar la insfraestructura mediante Github Actions (archivo infra.yml) en AWS (4ptos)
   
<details><summary>Infra/main.tf: clic aqui</summary>

```Yaml
Terraform used the selected providers to generate the following execution
plan. Resource actions are indicated with the following symbols:
 + create

Terraform will perform the following actions:

 # aws_db_instance.rds will be created
 + resource "aws_db_instance" "rds" {
     + address                               = (known after apply)
     + allocated_storage                     = 10
     + apply_immediately                     = false
     + arn                                   = (known after apply)
     + auto_minor_version_upgrade            = true
     + availability_zone                     = (known after apply)
     + backup_retention_period               = (known after apply)
     + backup_target                         = (known after apply)
     + backup_window                         = (known after apply)
     + ca_cert_identifier                    = (known after apply)
     + character_set_name                    = (known after apply)
     + copy_tags_to_snapshot                 = false
     + db_name                               = (known after apply)
     + db_subnet_group_name                  = (known after apply)
     + dedicated_log_volume                  = false
     + delete_automated_backups              = true
     + domain_fqdn                           = (known after apply)
     + endpoint                              = (known after apply)
     + engine                                = "mysql"
     + engine_lifecycle_support              = (known after apply)
     + engine_version                        = "8.0.35"
     + engine_version_actual                 = (known after apply)
     + hosted_zone_id                        = (known after apply)
     + id                                    = (known after apply)
     + identifier                            = "rds-cuadros"
     + identifier_prefix                     = (known after apply)
     + instance_class                        = "db.t3.micro"
     + iops                                  = (known after apply)
     + kms_key_id                            = (known after apply)
     + latest_restorable_time                = (known after apply)
     + license_model                         = (known after apply)
     + listener_endpoint                     = (known after apply)
     + maintenance_window                    = (known after apply)
     + master_user_secret                    = (known after apply)
     + master_user_secret_kms_key_id         = (known after apply)
     + monitoring_interval                   = 0
     + monitoring_role_arn                   = (known after apply)
     + multi_az                              = (known after apply)
     + nchar_character_set_name              = (known after apply)
     + network_type                          = (known after apply)
     + option_group_name                     = (known after apply)
     + parameter_group_name                  = (known after apply)
     + password                              = (sensitive value)
     + performance_insights_enabled          = false
     + performance_insights_kms_key_id       = (known after apply)
     + performance_insights_retention_period = (known after apply)
     + port                                  = (known after apply)
     + publicly_accessible                   = true
     + replica_mode                          = (known after apply)
     + replicas                              = (known after apply)
     + resource_id                           = (known after apply)
     + skip_final_snapshot                   = true
     + snapshot_identifier                   = (known after apply)
     + status                                = (known after apply)
     + storage_throughput                    = (known after apply)
     + storage_type                          = (known after apply)
     + tags_all                              = (known after apply)
     + timezone                              = (known after apply)
     + username                              = "***"
     + vpc_security_group_ids                = (known after apply)
   }

 # aws_elastic_beanstalk_application.webapp will be created
 + resource "aws_elastic_beanstalk_application" "webapp" {
     + arn         = (known after apply)
     + description = "Aplicación en Elastic Beanstalk"
     + id          = (known after apply)
     + name        = "eb-cuadros"
     + tags_all    = (known after apply)
   }

 # aws_elastic_beanstalk_environment.webapp_env will be created
 + resource "aws_elastic_beanstalk_environment" "webapp_env" {
     + all_settings           = (known after apply)
     + application            = "eb-cuadros"
     + arn                    = (known after apply)
     + autoscaling_groups     = (known after apply)
     + cname                  = (known after apply)
     + cname_prefix           = (known after apply)
     + endpoint_url           = (known after apply)
     + id                     = (known after apply)
     + instances              = (known after apply)
     + launch_configurations  = (known after apply)
     + load_balancers         = (known after apply)
     + name                   = "eb-env-cuadros"
     + platform_arn           = (known after apply)
     + queues                 = (known after apply)
     + solution_stack_name    = "64bit Amazon Linux 2023 v3.3.0 running .NET 8"
     + tags_all               = (known after apply)
     + tier                   = "WebServer"
     + triggers               = (known after apply)
     + version_label          = (known after apply)
     + wait_for_ready_timeout = "20m"

     + setting {
         + name      = "EnvironmentType"
         + namespace = "aws:elasticbeanstalk:environment"
         + value     = "SingleInstance"
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "IamInstanceProfile"
         + namespace = "aws:autoscaling:launchconfiguration"
         + value     = "LabInstanceProfile"
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "InstanceType"
         + namespace = "aws:autoscaling:launchconfiguration"
         + value     = "t3.micro"
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "RDS_DB_NAME"
         + namespace = "aws:elasticbeanstalk:application:environment"
         + value     = (known after apply)
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "RDS_HOSTNAME"
         + namespace = "aws:elasticbeanstalk:application:environment"
         + value     = (known after apply)
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "RDS_PASSWORD"
         + namespace = "aws:elasticbeanstalk:application:environment"
         + value     = "***"
           # (1 unchanged attribute hidden)
       }
     + setting {
         + name      = "RDS_USERNAME"
         + namespace = "aws:elasticbeanstalk:application:environment"
         + value     = "***"
           # (1 unchanged attribute hidden)
       }
   }

```
</details>
  
<details><summary>Infra/main.tf: clic aqui</summary>

```Yaml

provider "aws" {
region = "us-east-1"
}

variable "instance_type" {
default = "t3.micro" # Free Tier
}

variable "db_username" {
default = "marcelocuadros"
}

variable "db_password" {
default = "marcelo99++"
}

resource "aws_elastic_beanstalk_application" "webapp" {
name        = "eb-cuadros"
description = "Aplicación en Elastic Beanstalk"
}

resource "aws_elastic_beanstalk_environment" "webapp_env" {
name                = "eb-env-cuadros"
application         = aws_elastic_beanstalk_application.webapp.name
solution_stack_name = "64bit Amazon Linux 2023 v3.3.0 running .NET 8"

setting {
  namespace = "aws:elasticbeanstalk:environment"
  name      = "EnvironmentType"
  value     = "SingleInstance"
}

setting {
  namespace = "aws:autoscaling:launchconfiguration"
  name      = "InstanceType"
  value     = var.instance_type
}

setting {
  namespace = "aws:autoscaling:launchconfiguration"
  name      = "IamInstanceProfile"
  value     = "LabInstanceProfile"
}

setting {
  namespace = "aws:elasticbeanstalk:application:environment"
  name      = "RDS_HOSTNAME"
  value     = aws_db_instance.rds.address
}

setting {
  namespace = "aws:elasticbeanstalk:application:environment"
  name      = "RDS_DB_NAME"
  value     = aws_db_instance.rds.db_name
}

setting {
  namespace = "aws:elasticbeanstalk:application:environment"
  name      = "RDS_USERNAME"
  value     = var.db_username
}

setting {
  namespace = "aws:elasticbeanstalk:application:environment"
  name      = "RDS_PASSWORD"
  value     = var.db_password
}
}

resource "aws_security_group" "webapp_sg" {
name        = "cuadros-sg" 
description = "Security group para Elastic Beanstalk y MySQL"

# Permitir tráfico HTTP
ingress {
  from_port   = 80
  to_port     = 80
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}

ingress {
  from_port   = 443
  to_port     = 443
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"]
}

ingress {
  from_port   = 3306
  to_port     = 3306
  protocol    = "tcp"
  cidr_blocks = ["0.0.0.0/0"] # Permite acceso público a MySQL
}

egress {
  from_port   = 0
  to_port     = 0
  protocol    = "-1"
  cidr_blocks = ["0.0.0.0/0"]
}
}

resource "aws_db_instance" "rds" {
identifier             = "rds-cuadros"
engine                = "mysql"
engine_version        = "8.0.35" 
instance_class        = "db.t3.micro" 
allocated_storage     = 10  # Free Tier (mínimo permitido)
username             = var.db_username
password             = var.db_password
publicly_accessible  = true  # Permite conexiones externas
skip_final_snapshot  = true  # Evitar cargos extra
vpc_security_group_ids = [aws_security_group.webapp_sg.id]
}
```
</details>

<details><summary>Deploy.yml: clic aqui</summary>

```Yaml
name: Construcción infraestructura en AWS

on:
 push:
   branches: [ "main" ]
   paths:
     - 'infra/**'
     - '.github/workflows/deploy.yml'
 workflow_dispatch:

jobs:
 Deploy-infra:
   runs-on: ubuntu-latest
   steps:
     - uses: actions/checkout@v4

     - name: Configurar AWS CLI con credenciales temporales
       run: |
         aws configure set aws_access_key_id ${{ secrets.AWS_ACCESS_KEY_ID }}
         aws configure set aws_secret_access_key ${{ secrets.AWS_SECRET_ACCESS_KEY }}
         aws configure set aws_session_token ${{ secrets.AWS_SESSION_TOKEN }}
         aws configure set region ${{ secrets.AWS_REGION }}

     - name: Verificar conexión a AWS
       run: aws sts get-caller-identity

     - name: Crear terraform.tfvars
       run: |
         cd infra
         echo "db_username=\"${{ secrets.DB_USERNAME }}\"" > terraform.tfvars
         echo "db_password=\"${{ secrets.DB_PASSWORD }}\"" >> terraform.tfvars

     - name: Setup Terraform
       uses: hashicorp/setup-terraform@v3

     - name: Terraform Init
       id: init
       run: cd infra && terraform init 

     - name: Terraform Validate
       id: validate
       run: cd infra && terraform validate -no-color

     - name: Terraform Plan
       run: cd infra && terraform plan -no-color -out main.tfplan

     - name: Crear String Output
       id: tf-plan-string
       run: |
           TERRAFORM_PLAN=$(cd infra && terraform show -no-color main.tfplan)
           delimiter="$(openssl rand -hex 8)"
           echo "summary<<${delimiter}" >> $GITHUB_OUTPUT
           echo "## Terraform Plan Output" >> $GITHUB_OUTPUT
           echo "<details><summary>Click to expand</summary>" >> $GITHUB_OUTPUT
           echo "" >> $GITHUB_OUTPUT
           echo '```terraform' >> $GITHUB_OUTPUT
           echo "$TERRAFORM_PLAN" >> $GITHUB_OUTPUT
           echo '```' >> $GITHUB_OUTPUT
           echo "</details>" >> $GITHUB_OUTPUT
           echo "${delimiter}" >> $GITHUB_OUTPUT

     - name: Publish Terraform Plan to Task Summary
       env:
         SUMMARY: ${{ steps.tf-plan-string.outputs.summary }}
       run: |
         echo "$SUMMARY" >> $GITHUB_STEP_SUMMARY

     - name: Setup Infracost
       uses: infracost/actions/setup@v3
       with:
         api-key: ${{ secrets.INFRACOST_API_KEY }}

     - name: Infracost Breakdown
       run: |
         cd infra
         infracost breakdown --path . --format html --out-file infracost-report.html
         sed -i '19,137d' infracost-report.html
         sed -i 's/$0/$ 0/g' infracost-report.html

     - name: Convert HTML to Markdown
       id: html2markdown
       uses: rknj/html2markdown@v1.1.0
       with:
         html-file: "infra/infracost-report.html"

     - name: Upload Infracost Report
       run: |
         echo "## Infracost Report" >> $GITHUB_STEP_SUMMARY
         echo "${{ steps.html2markdown.outputs.markdown-content }}" >> infracost.md
         cat infracost.md >> $GITHUB_STEP_SUMMARY

     - name: Setup Graphviz
       uses: ts-graphviz/setup-graphviz@v2        

     - name: Setup inframap
       run: |
         curl -L -o /tmp/inframap.tar.gz "https://github.com/cycloidio/inframap/releases/download/v0.7.0/inframap-linux-amd64.tar.gz"
         tar -xzvf /tmp/inframap.tar.gz -C /tmp
         mv -v /tmp/inframap-linux-amd64 /usr/local/bin/inframap
         chmod +x /usr/local/bin/inframap

     - name: Generar diagrama de infraestructura
       run: |
         cd infra
         /usr/local/bin/inframap generate main.tf --raw | dot -Tsvg > inframap_aws.svg

     - name: Upload inframap
       id: inframap-upload-step
       uses: actions/upload-artifact@v4
       with:
         name: inframap_aws.svg
         path: infra/inframap_aws.svg

     - name: Generar diagrama de infraestructura detallado en formato Mermaid
       run: |
         cd infra
         echo "
         echo "```mermaid" >> inframap.mmd
         echo "graph TD;" >> inframap.mmd
         
         inframap generate main.tf --json | jq -r '
           .resources | to_entries[] | 
           "    " + .key + " -->|connected to| " + 
           (.value.providers[0] // "unknown") + 
           " : " + (.value.mode // "managed") + " : " + 
           (.value.type // "unknown") 
         ' >> inframap.mmd
         
         echo "```" >> inframap.mmd

     - name: Publicar diagrama detallado Mermaid en GitHub Summary
       run: |
         echo "
         cat infra/inframap.mmd >> $GITHUB_STEP_SUMMARY

     - name: Terraform Apply
       run: |
         cd infra
         terraform apply -auto-approve
```
</details>

2. Completar las vistas para los demas modelos Cuenta, Movimiento y Tipo. (2ptos)
3. Completar la documentación de las clases (DocFX). (2ptos)
4. Generar el diagrama Mermaid correspondiente al proyecto (dotnet tool install --global dll2mmd) (2ptos)
5. Generar la automatización del despliegue de los cambios en la aplicación (archivo deploy.yml) (4 ptos)
