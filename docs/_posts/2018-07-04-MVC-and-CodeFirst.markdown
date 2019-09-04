---
layout: post
title:  "MVC on dot net and EF Code First Resources"
date:   2018-07-04 02:46:36 -0400
categories: MVC dev
---
<h3>.net MVC Developer course</h3>

<a href="https://app.pluralsight.com/library/courses/aspdotnetcore-web-application-building/table-of-contents" target="_blank">https://app.pluralsight.com/library/courses/aspdotnetcore-web-application-building/table-of-contents
</a>

<h3>Code First:</h3>

<a href="https://coding.abel.nu/2012/03/ef-migrations-command-reference/
" target="_blank">https://coding.abel.nu/2012/03/ef-migrations-command-reference/
</a>

Code notes:

<b>Basic EF code first commands</b>

{% highlight cmd %}
Add-Migration X1
Remove-Migration
Update-Database
{% endhighlight %}

DbContext <b>c#</b> example:

{% highlight c# %}
using CVApp.Models;
using Microsoft.EntityFrameworkCore;

namespace CVApp.Data
{
    public class AppDbContext : DbContext
    {
        public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
        {

        }

        public DbSet<CVDetail> CVDetails { get; set; }
        public DbSet<DetailType> DetailTypes { get; set; }
        public DbSet<Language> Languages { get; set; }

        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            base.OnModelCreating(modelBuilder);

            modelBuilder.Entity<DetailType>().ToTable("DetailType");
            modelBuilder.Entity<Language>().ToTable("Language");
            modelBuilder.Entity<CVDetail>().ToTable("CVDetail");

        }
    }
}
{% endhighlight %}
