---
layout: post
title: Get the latest id which you insert into db just now
date: 2011-05-26 17:53
author: afeilulu
comments: true
categories: [program]
---
c# code
static public int AddProductCategory(string newName, string connString)
{
Int32 newProdID = 0;
string sql =
"INSERT INTO Production.ProductCategory (Name) VALUES (@Name); "
+ "SELECT CAST(scope_identity() AS int)";
using (SqlConnection conn = new SqlConnection(connString))
{
SqlCommand cmd = new SqlCommand(sql, conn);
cmd.Parameters.Add("@Name", SqlDbType.VarChar);
cmd.Parameters["@name"].Value = newName;
try
{
conn.Open();
newProdID = (Int32)cmd.ExecuteScalar();
}
catch (Exception ex)
{
Console.WriteLine(ex.Message);
}
}
return (int)newProdID;
}
