# Step 7: Conditionally Delete<a name="GettingStarted.NET.07"></a>

In this step of the [Microsoft \.NET and DynamoDB Tutorial](GettingStarted.NET.md), you try to delete a movie record with a condition that is not met, and the deletion fails\. Then, when the condition is changed so that it is met, the deletion succeeds\.

The `Main` function in `DynamoDB_intro` starts by creating a condition as follows\.

```
      Expression condition = new Expression();
      condition.ExpressionAttributeValues[":val"] = 5.0;
      condition.ExpressionStatement = "info.rating <= :val";
```

The `Main` function then passes the `Expression` as one of the parameters of `DeletingItem_async` and waits on it\. `DeletingItem_async` is implemented in the `07_DeletingItem.cs` file\.

```
using System;
using System.Threading.Tasks;
using Amazon.DynamoDBv2.DocumentModel;

namespace DynamoDB_intro
{
  public static partial class DdbIntro
  {
    public static async Task<bool> DeletingItem_async( Table table, int year, string title,
                                                       Expression condition=null )
    {
      Document deletedItem;
      // Create Primitives for the HASH and RANGE portions of the primary key
      Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;

      Console.WriteLine( "  -- Trying to delete the {0} movie \"{1}\"...", year, title );
      try
      {
        var delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
      }
      catch( Exception ex )
      {
        Console.WriteLine( "     FAILED to delete the movie item, for this reason:\n       {0}\n", ex.Message );
        return false;
      }

      Console.WriteLine( "     -- SUCCEEDED in deleting the movie record that looks like this:\n" +
                            deletedItem.ToJsonPretty( ) );
     return true;
    }
  }
}
```

`DeletingItem_async` in turn includes the condition `Expression` in a `DeleteItemOperationConfig` object that it passes to the DynamoDB `Table.DeleteItemAsync` method when it waits on it\.

Because the movie's rating is 6\.5, which is higher than 5\.0, the condition is not met, and the deletion fails\.

Then, when the `Main` function changes the rating threshold in the condition to 7\.0 instead of 5\.0, the deletion succeeds\.

## Next Step<a name="GettingStarted.NET.07.NextStep"></a>

[Step 8: Query a DynamoDB Table with \.NET](GettingStarted.NET.08.md)