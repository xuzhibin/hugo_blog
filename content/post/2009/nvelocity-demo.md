---
title: NVelocity直接使用字符串模板
date: 2009-04-18
categories:
- .net
tags:
- nvelocity
---


``` csharp
using System;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using NVelocity;
using NVelocity.App;
namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            string templates = "Hi $name $surname, The date is $date.";
            Dictionary oo = new Dictionary();
            oo.Add("name", "Joe");
            oo.Add("surname", "Smith");
            oo.Add("date", DateTime.Now.ToString("D"));
            Console.WriteLine(Fill(templates,oo));
            Console.ReadLine();

        }
        static public string Fill(string template, IDictionary values)
        {
            var engine = new VelocityEngine();
            engine.Init();
            var context = new VelocityContext();
            if (values != null)
                foreach (string k in values.Keys)
                {
                    context.Put(k, values[k]);
                }
            using (var writer = new StringWriter())
            {
                engine.Evaluate(context, writer, "", template);
                return writer.GetStringBuilder().ToString();
            }
        }
    }
}
```
