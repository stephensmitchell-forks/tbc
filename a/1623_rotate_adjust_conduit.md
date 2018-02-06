<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<link rel="stylesheet" type="text/css" href="bc.css">
<!--
<script src="run_prettify.js" type="text/javascript"></script>
<script src="https://google-code-prettify.googlecode.com/svn/loader/run_prettify.js" type="text/javascript"></script>
-->
<script src="https://cdn.rawgit.com/google/code-prettify/master/loader/run_prettify.js" type="text/javascript"></script>
</head>

<!---

- 13766538 [创建线管图形形状与实际情况不符]
  Rotate to Adjust Conduit End
  Conduit end will exceed the elbow while modifying conduit diameters before elbows are created
  [Q] one end of his specific conduit element will exceed the elbow”, see attached Observed.jpg and Expected.jpg.
  There are 5 conduit elements will be created with his codes, but one conduit is in different size and conduit type.
  After my investigation, I found the following:
  - It will show up if I changed conduit diameters before creating elbows with his codes. (Included the conduit element with different size and type)
  - Remove the creation codes of the different size and type one, but change diameters as usual, then this issue won’t appear.
  [A] Hi Eason, It happened to me as well on pipes and valves.
  What I’ve done to fix this is after the fitting creation I’ve applied a ElementTransformUtils.Rotate() passing a zero radians angle to the family instance.
  http://thebuildingcoder.typepad.com/blog/2014/06/refresh-element-graphics-display.html
  using (Transaction trans = new Transaction(Document, "虚假旋转线管"))
  {
    trans.Start();
    var axis = Line.CreateBound(XYZ.Zero, XYZ.BasisX);
    var ids = createdConduits.Select( c => c.Id )
    .ToList();
    
    ElementTransformUtils.RotateElements(Document, ids, axis, 0);
    trans.Commit();
  }  

- 13840360 [How do I create conduit between 2 connectors?]
  https://forums.autodesk.com/t5/revit-api-forum/how-do-i-create-conduit-between-2-connectors/m-p/7727929

Rotation adjusts and fixes conduit end #RevitAPI @AutodeskRevit #bim #dynamobim @AutodeskForge #ForgeDevCon http://bit.ly/rotationfixconduit

I listed several possible approaches to trigger a refresh of an element's graphics display.
Here is another one: Applying element rotation to adjust and fix conduit end...

--->

### Rotation Adjusts and Fixes Conduit End

Quite a while ago, I listed a bunch of possible approaches
to [refresh element graphics display](http://thebuildingcoder.typepad.com/blog/2014/06/refresh-element-graphics-display.html).

Here comes a new one to join the gang:


####<a name="2"></a>Applying Element Rotation to Adjust and Fix Conduit End

**Question:** I am creating a series of several conduit elements.

One conduit is in a different size and conduit type.

When I modify the conduit diameters before the elbow is created, one of the conduit ends exceeds the elbow.

The observed result looks like this:

<center>
<img src="img/conduit_end_observed.jpg" alt="Conduit end extending too far" width="400"/>
</center>

I am expecting it to look like this:

<center>
<img src="img/conduit_end_expected.jpg" alt="Conduit end adjusted" width="400"/>
</center>

If I remove the code to set the different size and type, and change diameters as usual, then this issue does not appear.

**Answer by Paolo Serra:** This happened to me as well on pipes and valves.

I fixed this after the fitting creation by applying a call to `ElementTransformUtils.Rotate` to the family instance, passing in a zero radians angle.

<pre class="code">
<span style="color:blue;">using</span>(&nbsp;<span style="color:#2b91af;">Transaction</span>&nbsp;trans&nbsp;=&nbsp;<span style="color:blue;">new</span>&nbsp;<span style="color:#2b91af;">Transaction</span>(&nbsp;doc&nbsp;)&nbsp;)
{
&nbsp;&nbsp;trans.Start(&nbsp;<span style="color:#a31515;">&quot;虚假旋转线管&quot;</span>&nbsp;);
&nbsp;&nbsp;<span style="color:blue;">var</span>&nbsp;ids&nbsp;=&nbsp;conduits.Select(&nbsp;c&nbsp;=&gt;&nbsp;c.Id&nbsp;).ToList();
&nbsp;&nbsp;<span style="color:blue;">var</span>&nbsp;axis&nbsp;=&nbsp;<span style="color:#2b91af;">Line</span>.CreateBound(&nbsp;<span style="color:#2b91af;">XYZ</span>.Zero,&nbsp;<span style="color:#2b91af;">XYZ</span>.BasisX&nbsp;);
&nbsp;&nbsp;<span style="color:#2b91af;">ElementTransformUtils</span>.RotateElements(&nbsp;doc,&nbsp;ids,&nbsp;axis,&nbsp;0&nbsp;);
&nbsp;&nbsp;trans.Commit();
}
</pre>


<!----
####<a name="3"></a>Creatng a Conduit Between Two Connectors

the [Revit API discussion forum](http://forums.autodesk.com/t5/revit-api-forum/bd-p/160) thread
on [how to create conduit between two connectors](https://forums.autodesk.com/t5/revit-api-forum/how-do-i-create-conduit-between-2-connectors/m-p/7727929):

**Queston:**

**Answer:**
---->