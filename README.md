# api-versioning

# what is api versioning?like how would you take care of api's in a mern stack

Assume your backend is running on:

GET http://localhost:3000/api/v1/users
{
  "version": "v1",
  "users": [
    {
      "name": "John",
      "email": "john@gmail.com"
    }
  ]
}

GET http://localhost:3000/api/v2/users
{
  "version": "v2",
  "users": [
    {
      "fullName": "John",
      "email": "john@gmail.com",
      "isActive": true
    }
  ]
}

Final Folder Structure
src/
├── app.js
├── routes/
│   ├── v1/
│   │   ├── index.js
│   │   └── user.routes.js
│   └── v2/
│       ├── index.js
│       └── user.routes.js
├── controllers/
│   ├── v1/
│   │   └── user.controller.js
│   └── v2/
│       └── user.controller.js


# STEP 1
# app.js
const express = require('express');
const app = express();

const v1Routes = require('./routes/v1');
const v2Routes = require('./routes/v2');

app.use(express.json());

// Mount versioned APIs
app.use('/api/v1', v1Routes);
app.use('/api/v2', v2Routes);

app.listen(3000, () => {
  console.log('Server running on port 3000');
});

What this means

/api/v1 → goes to routes/v1/index.js

/api/v2 → goes to routes/v2/index.js


# STEP 2
# routes/v1/index.js

const express = require('express');
const router = express.Router();

const userRoutes = require('./user.routes');

// when URL has /users → forward to user.routes.js
router.use('/users', userRoutes);

module.exports = router;


If request is:
/api/v1/users
app.js → /api/v1 → index.js → /users → user.routes.js

# Step 3: routes/v1/user.routes.js

const express = require('express');
const router = express.Router();

const { getUsersV1 } = require('../../controllers/v1/user.controller');

// GET /api/v1/users
router.get('/', getUsersV1);

module.exports = router;

# Step 4: controllers/v1/user.controller.js

exports.getUsersV1 = (req, res) => {
  res.json({
    version: 'v1',
    users: [
      { name: 'John', email: 'john@gmail.com' }
    ]
  });
};

# Step 5: routes/v2/index.js

const express = require('express');
const router = express.Router();

const userRoutes = require('./user.routes');
const express = require('express');
const router = express.Router();

const { getUsersV2 } = require('../../controllers/v2/user.controller');

// GET /api/v2/users
router.get('/', getUsersV2);

module.exports = router;

# step 6
const express = require('express');
const router = express.Router();

const { getUsersV2 } = require('../../controllers/v2/user.controller');

// GET /api/v2/users
router.get('/', getUsersV2);

module.exports = router;


# Step 7: controllers/v2/user.controller.js
exports.getUsersV2 = (req, res) => {
  res.json({
    version: 'v2',
    users: [
      {
        fullName: 'John',
        email: 'john@gmail.com',
        isActive: true
      }
    ]
  });
};


# FINAL REQUEST FLOW
GET /api/v1/users

app.js
→ /api/v1
→ routes/v1/index.js
→ /users
→ routes/v1/user.routes.js
→ controllers/v1/user.controller.js

GET /api/v2/users
app.js
→ /api/v2
→ routes/v2/index.js
→ /users
→ routes/v2/user.routes.js
→ controllers/v2/user.controller.js


“Both v1 and v2 have /users routes, but they are mounted under different version prefixes (/api/v1 and /api/v2), so Express routes requests to the correct versioned controller.”

