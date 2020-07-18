---
layout: post
title: Web Programming (CS 418)
categories: [HTML,CSS,Bootstrap,Linux,Apache,MySQL,PHP]
excerpt: Overview of Internet and World Wide Web; web servers and security, HTTP protocol; web application and design; server side scripts and database integration, and programming for the Web.
---

## Semester Project

Students will construct a single application over the duration of the semester and will be delivered in a series of milestones. This simulates a professional software development environment. We will develop in the LAMP environment: Linux, Apache, MySQL, and PHP. Emphasis will be on the integration of these components for a useful application; none will be exhaustively covered. We will use git for version control via GitHub and project submission, so the code you generate in the course will serve as a publicly available example of the experience you obtain from the course.

### The Milestones

#### Milestone 1

This milestone is the first step in building a fully operational search engine site. Your first task is to build the basic framework for the site. The topical focus of the site's questions is up to you (e.g., it can be a site about cars, dogs, programming, etc.) but the functionality must meet the milestone's requirements. Refer to your favorite search engine site for functionality and ideas of topics. Your topic does not need to be original, but you should be able to justify the usage cases and it should be ***ethical***.

*Base requirements*
1. The website should provide a search box at the landing page;
2. There should be a search button next to the search box;

	![BassLine Home Screen](/images/web-programming/Home-Screen.png)

3. Users must be able to register new accounts using email addresses;

	![Bassline Registration Page](/images/web-programming/Register-Test-User-Screen.png)

4. Users cannot register duplicate accounts using the same email address, or phone
number;

	![Bassline Duplicate Account Page](/images/web-programming/Duplicate-Account-Screen.png)

5. Users should be able to log into your website using the accounts they registered;

	![Bassline Login Page](/images/web-programming/Login-Screen.png)

6. The user login process must use the HTTP POST method;

	```html
	<div class="wrapper">
        <center>
            <h2>Login</h2>
            <p>Please fill in your credentials to login.</p>
        </center>
        <form action="<?php echo htmlspecialchars($_SERVER["PHP_SELF"]); ?>" method="post" autocomplete="on">
            <div class="form-group <?php echo (!empty($username_err)) ? 'has-error' : ''; ?>">
                <label>Username</label>
                <input type="text" name="username" class="form-control" value="<?php echo $username; ?>">
                <span class="help-block"><?php echo $username_err; ?></span>
            </div>    
            <div class="form-group <?php echo (!empty($password_err)) ? 'has-error' : ''; ?>">
                <label>Password</label>
                <input type="password" name="password" class="form-control">
                <span class="help-block"><?php echo $password_err; ?></span>
            </div>
            <div class="g-recaptcha" data-sitekey="6Lc76cMUAAAAAPBkP3F9beVZzy3OpVyGYt1IjbI7"></div>
            <div class="form-group">
                <input type="submit" class="btn formbtn" value="Login" style="line-height: 15px;">
            </div>
            <center>
                <p>Don't have an account? <a href="register.php">Sign up now</a>.</p>
                <p><a href="reset.php">Forgot your password?</a></p>
            </center>
        </form>
    </div> <!-- END OF CONTAINER -->	
	```

	```php
	// Validate credentials
    if(empty($username_err) && empty($password_err) && !empty($captcha)){
        // Prepare a select statement
        //$sql = "SELECT id, username, password, active FROM users WHERE username = ? AND active='1'";
        $sql = "SELECT id, username, password FROM users WHERE username = ?";
        if($stmt = mysqli_prepare($link, $sql)){
            // Bind variables to the prepared statement as parameters
            mysqli_stmt_bind_param($stmt, "s", $param_username);
            // Set parameters
            $param_username = $username;
            // Attempt to execute the prepared statement
            if(mysqli_stmt_execute($stmt)){
                // Store result
                mysqli_stmt_store_result($stmt);
                // Check if username exists, if yes then verify password
                if(mysqli_stmt_num_rows($stmt) == 1){                    
                    // Bind result variables
                    mysqli_stmt_bind_result($stmt, $id, $username, $hashed_password);
                    if(mysqli_stmt_fetch($stmt)){
                        if(password_verify($password, $hashed_password)){
                            // Password is correct, so start a new session
                            session_start();
                            // Store data in session variables
                            $_SESSION["loggedin"] = true;
                            $_SESSION["id"] = $id;
                            $_SESSION["username"] = $username; 
                            header("location: welcome.php");
                        } else{
                            // Display an error message if password is not valid
                            $password_err = "The password you entered was not valid.";
                        }
                    }
                } else{
                    // Display an error message if username doesn't exist
                    $username_err = "Please check information is entered correctly and account is verified.";
                }
            } else{
                echo "Oops! Something went wrong. Please try again later.";
            }
        }
        // Close statement
        mysqli_stmt_close($stmt);
    }
```

7. Users should be able to reset their passwords if they forget it;
8. The website should have a homepage for each user, where they can view their profiles,
change passwords, and update demographic information.

	![Bassline Profile Page](/images/web-programming/Test-User-Profile-Screen.png)

#### Milestone 2

In this milestone, you will build the search function in your search engine using elasticsearch and PHP or JavaScript.

*Base requirements:*

1. The website should index at least 1000 documents;

	![Kibana Framework view of ElasticSearch](/images/web-programming/Kibana-ElasticSearch-Screen.png)

2. The search engine accepts a text query in the search box;

	![Text Query in a search box](/images/web-programming/Home-Page-Text-Search.png)

3. The search engine should return search results on a separate page;
4. The search engine result page (SERP) should contain a search box;

	![Results of a search](/images/web-programming/Results-Page.png)

5. The search engine can prevent XSS vulnerability by removing tags existing in the query;

	```php
	require './es/vendor/autoload.php'; //Composer autoload
	$client = Elasticsearch\ClientBuilder::create()->build();
	$MAX_SIZE=10000;
  	if (!empty($_POST['generalSearch'])) {
    	$keyword = filter_var($_POST['generalSearch'], FILTER_SANITIZE_STRING);
    	$json = '{
    		"size": "'.$MAX_SIZE.'",
    		"query": {
		    	"bool": {
		      		"must": [
		        		{
		          		"multi_match": {
		            		"query": "'.$keyword.'",
		            		"type": "best_fields",
							"fields": [ "Performer", "Song" ],
							"operator": "and",
							"fuzziness": "AUTO"
		          			}
		        		}
		      		],
			    	"filter": {
			        	"match": {
			          		"Weeks on Chart": "1"
			        	}
			      	}
			    }
			  },
			  "sort": [
			    {
			      "WeekID": {
			        "order": "desc"
			      }
			    }
			  ]
		}';
		  $params = [
		     'type' => '_search',
		     'body'=> $json
		 ];
    	$query = $client->search($params);
}
if($query['hits']['total'] >=1 ) {
    $results = $query['hits']['hits'];
    $numHits = count($results);
}
```

6. The website has an “Advanced Search” in which users can specify more information;

	![User view of Advanced Search](/images/web-programming/Advanced-Search-Function.png)

7. The advanced search should return results satisfying multiple specifications.

	![Results of Advanced Search](/images/web-programming/Advanced-Search-Results.png)

#### Milestone 3

In this milestone, you will enhance the search engine by adding more features to make searching more secure, transparent, and user friendly.

*Base requirements:*
1. The search engine can return paginated results;
2. The search engine can highlight results that contain search terms;
3. The SERP should display the actual term (after sanitization) shown on top;

	![Results of a search](/images/web-programming/Results-Page.png)

4. Users can click each item on SERP and go to either an external link or a page containing
more information of the item;

	![Clicking a Link](/images/web-programming/External-Page-Click.png)
	![Results of a link click](/images/web-programming/External-Click-Results.png)

5. Users have to login first to save search history to their profiles;

	<embed src="/images/web-programming/Cannot-Save-Without-Login.mp4">

6. Users can save items in search result to their profiles;

	> At time of project conclusion, this feature returned results from the MySQL database and found the information within ElasticSearch. With the ElasticSearch version upgrade, this item is currently under work to be fixed. Here is the completed code that loads the results into the MySQL Database.

	```php
	// Check if the user is logged in, if not then redirect him to login page
    if(!isset($_SESSION["loggedin"]) || $_SESSION["loggedin"] !== true){
        header("location: login.php");
    }
    else
    {
        $err = 0;
        for ($i=0; $i < count($_POST['doc_id']); $i++ ) {
            $docid = ($_POST['doc_id'][$i]);
            $query = $query."SELECT id FROM users WHERE doc_id = '$docid'".";";
        }
        if (mysqli_multi_query($link, $query))
        {
            do{
                // Store first result set
                if ($result=mysqli_store_result($link)) {
                    if(mysqli_num_rows($result) == 1){
                        $err++;
                    }
                    // Free result set
                    mysqli_free_result($result);
                }
            } while (mysqli_next_result($link));
            // Close statement
            mysqli_stmt_close($result);
        } 
        if ($err == 0) {
            for ($i=0; $i < count($_POST['doc_id']); $i++ ) {
                $docID = ($_POST['doc_id'][$i]);
                $param_user_name = $_SESSION["username"];
                $sql = $sql."INSERT INTO saved_search (doc_id, user_name) VALUES ('$docID', '$param_user_name')".";";
            }
            if (mysqli_multi_query($link, $sql)) {
                header("location: welcome.php");
            } else {
                echo "Error: " . $sql . "<br>" . $link->error;
            }
            $link->close();
        }
        else
        {
            echo "<br>A song you have chosen is already present. Please choose other songs to save.";
            echo '<br><br><a href="welcome.php" class="btn btn-success" role="button">Return to Welcome page.</a>';
        }
    }
    ```

7. reCAPTCHA should be used for both the logging in and the signing up page;

	![Bassline Registration Page](/images/web-programming/Register-Test-User-Screen.png)
	![Bassline Login Page](/images/web-programming/Login-Screen.png)

#### Milestone 4

In this milestone, you will enhance the search engine by adding more features to make searching more secure, transparent, and user friendly.

*Base requirements:*
1. Users can delete items from their favorite list;

	```php
	// Check if the user is logged in, if not then redirect him to login page
  if(!isset($_SESSION["loggedin"]) || $_SESSION["loggedin"] !== true){
      header("location: login.php");
  }
  else
  {
    for ($i=0; $i < count($_POST['doc_id']); $i++ ) {
        $docID = ($_POST['doc_id'][$i]);
        $param_user_name = $_SESSION["username"];
        $sql = $sql."DELETE FROM saved_search WHERE `doc_id` = '".$docID."' AND `user_name` = '".$param_user_name."';";
    }
    if (mysqli_multi_query($link, $sql)) {
        header("location: welcome.php");
    } else {
        echo "Error: " . $sql . "<br>" . $link->error;
    }
    $link->close();
  }
  ```

2. Items in the favorite lists should be descriptive (can’t be just a link) and are linked to and
external page or a summary page of the item;

	```html
	  // Prepare query
	  $table = "saved_search";
	  $username = $_SESSION["username"];
	  $sql = "SELECT doc_id FROM $table WHERE user_name = '$username'";
	  // Execute query
	  $query_result = $link->query($sql);
	  if (!$query_result) {
	      echo "Query is wrong: $sql";
	      die;
	  }
	  $result_array = array();
	  $cnt = 0;
	  while($line = $query_result->fetch_assoc())
	  {
	      foreach ($line as $cell) {
	        $result_array[] = $cell;
	        $cnt++;
	      }
	  }
	  for ($i = 0; $i < $cnt; $i++)
	  {
	    if ($i != $cnt-1)
	    {
	      $docs = $docs.'{"_index" : "music","_id" : "'.$result_array[$i].'"},';
	    }
	    else
	    {
	      $docs = $docs.'{"_index" : "music","_id" : "'.$result_array[$i].'"}';
	    }
	  }
	  require './es/vendor/autoload.php'; //Composer autoload
	  $client = Elasticsearch\ClientBuilder::create()->build();
	  $MAX_SIZE=10000;
	  $json = '{
	    "docs" : ['.$docs.']}';
	  $params = [
	     'body'=> $json
	 ];
	  $query = $client->mget($params);
	  echo $query;
	if($query['docs'][0]['found'] == true) {
	    $results = $query['docs'];
	}
	if (!empty($results)) { ?>
	  <div class="container">
	  <form name="resultsTable" action="deleteResults.php" method="POST">
	  <table id="example" class="display" cellspacing="0" style="width:100%">
	    <thead>
	      <tr align="center">
	        <th></th>
	          <th>Billboard 100 <br />Week ID</th>
	          <th>Performer</th>
	          <th>Song</th>
	          <th>Weeks On Chart</th>
	          <th>Week Position</th>
	          <th>Previous Week Position</th>
	          <th>Peak Position</th>
	      </tr>
	  </thead>
	  <tbody>
	      <?php foreach ($results as $r) { ?>
	          <tr align="center">
	            <td><center><input type="checkbox" name="doc_id[]" value="<?php echo $r['_id'];?>"></center></td>
	              <td><?php echo $r['_source']['WeekID'] ?></td>
	              <td><?php echo $r['_source']['Performer'] ?></td>
	              <td><?php echo '<a target="_blank" style="color: #7132FF;" href="'.$r['_source']['url'].' ">'.$r['_source']['Song'].'</a>' ?></td>
	              <td><?php echo $r['_source']['Weeks on Chart'] ?></td>
	              <td><?php echo $r['_source']['Week Position'] ?></td>
	              <td><?php echo $r['_source']['Previous Week Position'] ?></td>
	              <td><?php echo $r['_source']['Peak Position'] ?></td>
	          </tr>
	      <?php } ?>
	  </tbody>
	  </table>
	<center><button type="submit" class="btn btn-hg btn-success" id="modal-btn">Delete</button></center>
	</form>
	</div>
	<script>
	  $(document).ready(function() {
	  $('#example').DataTable( {
	      select: {
	          style: 'multi'
	      }
	    } );
	  } );
	</script>
	<?php } else { echo "<center><p>No result found.</p></center>"; } ?>
```

> Please note that these two requirements are also based upon the previous Milestone's view of the saved search results. At time of project conclusion, these items worked as intended. Once I get the results to return correctly, this section will be updated to view the results. Thank you for your patience.