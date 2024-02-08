---
title: "Accessing MongoDB from Nextjs"
datePublished: Fri Oct 22 2021 22:18:04 GMT+0000 (Coordinated Universal Time)
cuid: clsd6qz3e00050ajq6h9e5pqh
slug: accessing-mongodb-from-nextjs
canonical: https://roderickkennedy.com/dbgdiary/accessing-mongodb-from-nextjs

---

Accessing MongoDB from Nextjs
=============================

22 Oct

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Following the example of

[www.mongodb.com/developer/how-to/nextjs-with-mongodb/](https://www.mongodb.com/developer/how-to/nextjs-with-mongodb/) you soon run into difficulty - as of October 2021, the version of the nextjs Mongo integration initialized by

    npx create-next-app --example with-mongodb mflix

as the tutorial expects to find the file util/mongodb.js and the function connectToDatabase it contains, which is missing - because create-next-app here checks out [github.com/vercel/next.js/tree/canary/examples/with-mongodb](https://github.com/vercel/next.js/tree/canary/examples/with-mongodb) to the “canary” branch. You have to either check out the branch “master” of that repo, or manually add the mongodb.js file from that branch into the project it created.  
  
Then, the tutorial skips over a lot of crucial information about how to use the API. The answer to obtaining a single result from the API is to create a file e.g. \[movie\_id\].js in pages/api/movies, and fill it with:

    import { connectToDatabase } from "../../../util/mongodb";

    const {ObjectId} = require('mongodb');

    export default async function handler(req, res)

    {

    	const { movie_id } = req.query;

    	const { db } = await connectToDatabase();

    	var o_id = new ObjectId(movie_id);

    	const movie_info = await db

    	  .collection("movies")

    	  .find({'_id' : o_id})

    	  .toArray();

    	res.json(movie_info);

      }

The key here is getting the definition of ObjectId from the mongodb node module, initializing o\_id as an ObjectId from the 24-char movie idm then using it in the find() function of the collection object.

Overall, I’m thinking Mongo/Nextjs is insufficiently documented for my needs - my search for a node data backend continues.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Accessing MongoDB from Nextjs
=============================

22 Oct

Written By [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)

Following the example of

[www.mongodb.com/developer/how-to/nextjs-with-mongodb/](https://www.mongodb.com/developer/how-to/nextjs-with-mongodb/) you soon run into difficulty - as of October 2021, the version of the nextjs Mongo integration initialized by

    npx create-next-app --example with-mongodb mflix

as the tutorial expects to find the file util/mongodb.js and the function connectToDatabase it contains, which is missing - because create-next-app here checks out [github.com/vercel/next.js/tree/canary/examples/with-mongodb](https://github.com/vercel/next.js/tree/canary/examples/with-mongodb) to the “canary” branch. You have to either check out the branch “master” of that repo, or manually add the mongodb.js file from that branch into the project it created.  
  
Then, the tutorial skips over a lot of crucial information about how to use the API. The answer to obtaining a single result from the API is to create a file e.g. \[movie\_id\].js in pages/api/movies, and fill it with:

    import { connectToDatabase } from "../../../util/mongodb";

    const {ObjectId} = require('mongodb');

    export default async function handler(req, res)

    {

    	const { movie_id } = req.query;

    	const { db } = await connectToDatabase();

    	var o_id = new ObjectId(movie_id);

    	const movie_info = await db

    	  .collection("movies")

    	  .find({'_id' : o_id})

    	  .toArray();

    	res.json(movie_info);

      }

The key here is getting the definition of ObjectId from the mongodb node module, initializing o\_id as an ObjectId from the 24-char movie idm then using it in the find() function of the collection object.

Overall, I’m thinking Mongo/Nextjs is insufficiently documented for my needs - my search for a node data backend continues.

 [Roderick Kennedy](https://roderickkennedy.com/dbgdiary?author=5f08d2770b281846bf04ee3b)