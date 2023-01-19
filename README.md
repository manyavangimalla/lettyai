# lettyai
#
#
#
#
#
Write a simple React component that renders a form with a text input and a submit button. When the form is submitted, the component should make a POST request to a Node.js server with the contents of the text input.
Answer ---------------------------------------------------------------------------------------------------------------------------------------------------------------- a text input and a submit button and makes a POST request to a Node.js server with the contents of the text input when the form is submitted:
     import React, { useState } from 'react';
import axios from 'axios';

const Form = () => {
  const [inputValue, setInputValue] = useState('');

  const handleSubmit = (event) => {
    event.preventDefault();
    axios.post('http://localhost:3000/submit', { input: inputValue })
      .then(response => console.log(response))
      .catch(error => console.log(error));
  }

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="text"
        value={inputValue}
        onChange={e => setInputValue(e.target.value)}
      />
      <button type="submit">Submit</button>
    </form>
  );
};

export default Form;
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
Write a Node.js server that can handle the POST request from the React component in question 1. The server should save the received data in a MongoDB database.
answer:
const express = require('express');
const mongoose = require('mongoose');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

mongoose.connect('mongodb://localhost:27017/mydb', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const InputSchema = new mongoose.Schema({
  input: String,
});

const Input = mongoose.model('Input', InputSchema);

app.post('/submit', (req, res) => {
  const newInput = new Input({
    input: req.body.input,
  });

  newInput.save((err) => {
    if (err) {
      res.send(err);
    } else {
      res.send('Successfully saved input to database.');
    }
  });
});

app.listen(3000, () => {
  console.log('Server listening on port 3000');
});

--------------------------------------------------------------------------------------------------------------------------------------------------------------------
Write a React component that fetches data from the Node.js server and displays it in a table.
Answer:

import React, { useState, useEffect } from 'react';
import axios from 'axios';

const DataTable = () => {
  const [data, setData] = useState([]);

  useEffect(() => {
    axios.get('http://localhost:3000/data')
      .then(response => setData(response.data))
      .catch(error => console.log(error));
  }, []);

  return (
    <table>
      <thead>
        <tr>
          <th>Input</th>
        </tr>
      </thead>
      <tbody>
        {data.map((item, index) => (
          <tr key={index}>
            <td>{item.input}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
};

export default DataTable;
Using response data is then stored in the data state variable, which is used to render the table rows.
--------------------------------------------------------------------------------------------------------------------------------------------------------------------
Write a Node.js endpoint that takes in a MongoDB _id as a parameter and returns the corresponding document from the database in question 2.
Answer:
const express = require('express');
const mongoose = require('mongoose');
const { ObjectId } = mongoose.Types;

const app = express();

mongoose.connect('mongodb://localhost:27017/mydb', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const InputSchema = new mongoose.Schema({
  input: String,
});

const Input = mongoose.model('Input', InputSchema);

app.get('/data/:id', (req, res) => {
  if (!ObjectId.isValid(req.params.id)) {
    res.status(400).send('Invalid id');
    return;
  }
  Input.findById(req.params.id, (err, data) => {
    if (err) {
      res.send(err);
    } else if (!data) {
      res.status(404).send('Data not found');
    } else {
      res.send(data);
    }
  });
});

app.listen(3000, () => {
  console.log('Server listening on port 3000');
});
--------------------------------------------------------------------------------------------------------------------------------------------------------------------








THE END
