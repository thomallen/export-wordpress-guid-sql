export-wordpress-guid-sql
=========================

One feature I've either missed over the years or doesn't exist, is the ability to add more meta data to imported posts. Here is the scenario:

New WordPress site
Importing old WordPress content into new site
Want to add a new category to all posts (or tag)
User custom template page to display new posts (category-foo.php)

So how do we solve this issue? Here is what I did. This may not be the best or optimum way, and as a disclaimer, unless you fully understand databases, sql syntax, and how WordPress ties posts and meta data, this may be a little more than you want to tackle (but I can help you of course!).

Here is is how I started. Wrote a quick PHP script to read the WordPress XML file from site one and display the GUID tag value on the screen. Since I don't know what the new post ID is after the fact, and the GUID is a field in both the XML and the database, I used that. Also, because I'm lazy at times, I included writing out the actual SQL code I'll use to build a SELECT statement.



Now that I have this code, I run the query in phpMyAdmin, and I get a result set full of Insert statements. I use the Export feature to CSV for Excel, and then open in my favorite code editor Coda. The Insert statements are important because it's how I'm going to manually add the category. This little query returns the post ID and the wp_terms ID.

But before we crate all this cool code, I had to do a little leg work. In order to make this work you need two things, the post ID and the term (category) ID. Armed with those two values, I can add records to wp_term_relationships. First I needed to create the new category called Blog. After creating that, I went into phpMyAdmin and looked in the wp_terms table, and found the ID of the category Blog. Noting the ID I added it to the concant() sql function used in creating the INSERT statements along with the post ID from wp_posts.

The output in the CSV file looks something like this: INSERT INTO wp_term_relationships VALUES (2082,838)

Once in Coda, I did a string replace of the closing paren ) with ,0); so the INSERT statement closed correctly. The statment now looks like this: INSERT INTO wp_term_relationships VALUES (2082,838,0);

I copied all the lines in the CSV file, pasted them into the SQL window in phpMyAdmin, and clicked GO!

The result was 341 record updates with the new relationship of Blog. Go into the Admin and click on the Category link under posts, and to the right of Blog, I saw the correct number of entries. Click on the entries number gives you all the posts who have the category Blog. Voila!

This process seems convoluted and cumbersome, but it's not meant to be accomplished by everyone. What I did here was basically violate the code of conduct for WordPress developers by manually adding values to the database instead of letting the WordPress framework do it. My next assignment is to look for some type of method that would allow me to do this. What I really want is the ability to add additional meta data at the time I import. But that's for another day. And maybe someone has already figured this out, and I would love to see how that works.
