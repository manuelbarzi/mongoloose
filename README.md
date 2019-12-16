# Fake Mongoose that stores data in local JSON files

## Usage

```js
const mongoloose = require('mongoloose')
const { Schema, ObjectId, model } = mongoloose 

const User = model('User', new Schema({
    username: {
        type: String,
        required: true
    },
    password: {
        type: String,
        required: true
    },
    date: {
        type: Date
    }
}))

const Task = model('Task', new Schema({
    subject: {
        type: String,
        required: true
    },
    body: {
        type: String,
        required: true
    },
    date: {
        type: Date
    },
    status: {
        type: Number,
        default: 0
    },
    user: {
        type: ObjectId,
        required: true,
        ref: 'User'
    }
}))


;(async () => {
    try {
        await mongoloose.connect() // if you want to locate database in a different folder just pass a folder path here (ex: mongoloose.connect('path-to-my-database-folder')

        const user = new User({ username: 'peter', password: '123', date: new Date })

        await user.save()

        console.log('// saved user =>')
        console.dir(user)

        user.username = 'petra'

        await user.save()

        console.log('// same user modified and resaved =>')
        console.dir(user)

        const user2 = User.findById(user.id)

        console.log('// user found by id =>')
        console.dir(user2)

        console.log('// user and user2 documents must be different instances =>', user.document !== user2.document)
        console.log('// user must be an instance of User =>', user instanceof User)
        console.log('// user2 must be an instance of User =>', user2 instanceof User)

        const task = new Task({ subject: 'Hello, World!', body: 'blah blah blah', user: user._id })
    
        await task.save()

        console.log('// saved task =>')
        console.dir(task)

        task.populate('user')

        console.log('// same task with user populated =>')
        console.dir(task)
    } catch (error) {
        console.error(error)
    }
})()
```