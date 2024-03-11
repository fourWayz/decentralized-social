# A Step-by-Step Guide to Building a Decentralized Social Media Platform on CELO Alfajores

## Table of Contents:

1. [Introduction](#introduction)
2. [Smart Contract Design](#smart-contract-design)
3. [User Registration](#user-registration)
4. [Creating Posts](#creating-posts)
5. [Interacting with Posts](#interacting-with-posts)
6. [Viewing Posts and Comments](#viewing-posts-and-comments)
7. [Complete Code](#complete-code)
8. [Setting up Remix for CELO Alfajores](#setting-up-remix-for-celo-alfajores)

## Prerequisites
- Basic understanding of Solidity programming language
- Celo testnet faucet [faucet](https://faucet.celo.org/alfajores)
- Access to Remix IDE [Remix](https://remix.ethereum.org)

  
## Introduction
In this article, you will learn how to develop a decentralized social media platform. Leveraging the power of the most powerful smart contracts language, Solidity, and the user-friendly Remix IDE, we will explore the process of developing and deploying this blockchain-based social media ecosystem. 

Throughout this guide, we will give a deep dive into the fundamental aspects of writing smart contracts. We will handle user registration, post creation, interaction mechanisms such as liking and commenting, and finally, setting up Remix for deployment on the CELO Alfajores network.

## Smart Contract Design

  ### Explanation of data structures (User, Post, Comment)
          
  ```solidity
     contract SocialMedia {

      address public owner;
      struct User {
          string username;
          address userAddress;
          bool isRegistered;
      }
  
      mapping(address => User) public users;
  
      struct Post {
          address author;
          string content;
          uint256 timestamp;
          uint256 likes;
          uint256 commentsCount;
      }
  
      struct Comment {
          address commenter;
          string content;
          uint256 timestamp;
      }
  
      mapping(uint256 => mapping(uint256 => Comment)) public postComments;
      mapping(uint256 => uint256) public postCommentsCount;
  
      Post[] public posts;
}
  ```
  
  The `SocialMedia` contract facilitates interactions on the decentralized social media platform. It defines the structure for a social media platform where users can register, make posts, and comment on posts.
  It provides functionality to store user information, posts, and comments, and retrieve them as needed. Additionally, it includes variables and mappings to keep track of post comments and their counts.
  The `owner` : An address variable to store the address of the owner of the contract.
  
  The `User` struct represents registered users on the platform, containing fields for username, address, and registration status.
  
  The `Post` struct stores information about each post, including the author's address, content, timestamp, number of likes, and an array of comments.
  It contains fields such as author (address of the author), content (the actual content of the post), timestamp (time when the post was made), likes (number of likes on the post), and commentsCount (number of comments on the post).
  
  The `Comment` struct represents individual comments, containing fields for the commenter's address, content, and timestamp. It contains fields like commenter (address of the commenter), content (the actual content of the comment), and timestamp (time when the comment was made).
  
  The `postComments` mapping is used to store comments for each post where the first key is the post's index, and the second key is the comment's index.
  
  The `postCommentsCount` mapping keeps track of the number of comments for each post.
  
  The `posts` array stores instances of the Post struct, representing individual posts on the decentralized social media platform.
  
### Description of events and modifiers
```solidity
    event UserRegistered(address indexed userAddress, string username);
```
This event is emitted when a new user is successfully registered on the platform. It returns the registered user's address (`userAddress`) and the chosen username (`username`).

```solidity
    event PostCreated(address indexed author, string content, uint256 timestamp);
```
This event is emitted when a new post is created by a user on the platform. It returns the address of the post's author (`author`), the content of the post (`content`), and the timestamp of creation (`timestamp`).

```solidity
    event PostLiked(address indexed liker, uint256 indexed postId);
```
This event is emitted when a user likes a post on the platform. It provides visibility into user engagement and returns
the address of the user who liked the post (`liker`) and the index of the liked post (`postId`).

```solidity
    event CommentAdded(address indexed commenter, uint256 indexed postId, string content, uint256 timestamp);
```
This event is emitted when a user adds a comment to a post on the platform and returns the address of the commenter (commenter), the index of the post being commented on (postId), the content of the comment (content), and the timestamp of comment creation (`timestamp`).

```solidity
    modifier onlyRegisteredUser() {
        require(users[msg.sender].isRegistered, "User is not registered");
        _;
    }
```
This modifier restricts access to functions to only registered users on the platform and ensures that only users who have completed the registration process can perform certain actions, such as creating posts or adding comments.
If an unregistered user attempts to call a function with this modifier, the transaction will be reverted with an error message indicating that the user is not registered.

```solidity
    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this function");
        _;
    }
```
This modifier restricts access to functions to the contract owner. It grants special privileges to the contract owner, such as administrative functions or contract upgrades.
If a caller who is not the contract owner attempts to call a function with this modifier, the transaction will be reverted with an error message indicating that only the owner can call the function.

```solidity
    constructor() {
        owner = msg.sender;
    }
```
Constructor is a special function that is executed only once during contract deployment. In this contract, the constructor initializes the contract owner to the address of the deployer (`msg.sender`).

## User Registration
   ### Implementation details of user registration functionality
   ```solidity
      function registerUser(string memory _username) external {
        require(!users[msg.sender].isRegistered, "User is already registered");
        require(bytes(_username).length > 0, "Username should not be empty");

        users[msg.sender] = User({
            username: _username,
            userAddress: msg.sender,
            isRegistered: true
        });

        emit UserRegistered(msg.sender, _username);
    }
   ```

   ### Explanation of the `registerUser` function
   This function is declared with the `registerUser` name, taking a single argument `_username` of type string which represents the username of the user registering. It is declared as `external`, meaning it can be called from outside the contract. It ensures that users can register on the platform with a unique username and provides transparency by emitting an event upon successful registration. 
   Additionally, it enforces security by verifying that the user is not already registered and that the provided username is not empty.
   
   - `require(!users[msg.sender].isRegistered, "User is already registered")` : This line ensures that the user calling the function (msg.sender) is not already 
    registered. It checks if the `isRegistered` flag in the users mapping for the caller's address is false. If it's true, meaning the user is already registered, 
    it will revert the transaction with the error message "User is already registered".

   - `require(bytes(_username).length > 0, "Username should not be empty")` : This line ensures that the `_username` parameter passed to the function is not empty. It checks if the length of the byte representation of the username is greater than 0. If it's not, it will revert the transaction with the error message "Username should not be empty".

   - `users[msg.sender] = User({username: _username, userAddress: msg.sender, isRegistered: true})` : If the input validation passes, a new User struct is created and stored in the users mapping with the caller's address (msg.sender) as the key. The username field of the struct is set to the `_username` parameter passed to the function, the `userAddress` is set to `msg.sender`, and the `isRegistered` flag is set to true, indicating that the user is now registered. 

  - `emit UserRegistered(msg.sender, _username)` : After successfully registering the user, an event `UserRegistered` is emitted. This allows external parties to listen for this event and take appropriate actions, such as updating UIs or performing additional logic.

## Creating Posts
   ### Implementation details of creating posts
   ```solidity
        function createPost(string memory _content) external onlyRegisteredUser {
        require(bytes(_content).length > 0, "Content should not be empty");

        posts.push(Post({
            author: msg.sender,
            content: _content,
            timestamp: block.timestamp,
            likes: 0,
            commentsCount: 0
        }));

        emit PostCreated(msg.sender, _content, block.timestamp);
    }
   ```
   ### Description of the `createPost` function
   This function ensures that registered users can create posts with non-empty content and provides transparency by emitting an event upon successful post creation. Additionally, it enforces security by restricting access to registered users only.

  - `onlyRegisteredUser`: This modifier ensures that only registered users can create posts. It's assumed to be defined elsewhere in the contract.
  - `require(bytes(_content).length > 0, "Content should not be empty")`: Ensures that the content of the post is not empty. It checks the length of the 
  - `_content` string and requires it to be greater than 0 bytes. 
  - `posts.push(...)`: Adds a new post  to the `posts` array. It creates a new `Post` struct with the following parameters:
    - `author`: The address of the user creating the post (`msg.sender`).
    - `content`: The content of the post, provided as input to the function (`_content`).
    - `timestamp`: The current block timestamp, indicating when the post was created (`block.timestamp`).
    - `likes`: Initialized to 0, indicating the number of likes on the post.
    - `commentsCount`: Initialized to 0, indicating the number of comments on the post.
  - `emit PostCreated(msg.sender, _content, block.timestamp)`: Emits an event `PostCreated`, indicating that a new post has been created. It includes the address of the author (`msg.sender`), the content of the post (`_content`), and the timestamp when the post was created (`block.timestamp`).

## Interacting with Posts
  ### Liking posts
  ```solidity
 function likePost(uint256 _postId) external onlyRegisteredUser {
        require(_postId < posts.length, "Post does not exist");

        Post storage post = posts[_postId];
        post.likes++;

        emit PostLiked(msg.sender, _postId);
    }
  ```
  ### Explanation of `likePost`
  This function ensures that registered users can like posts and emit an event upon successful post like. Also, 
  it enforces security by restricting access to registered users only and ensuring that the specified post exists before allowing a like action.

  - `onlyRegisteredUser`: This modifier ensures that only registered users can like posts. It's assumed to be defined elsewhere in the contract.  
  - `require(_postId < posts.length, "Post does not exist")`: Checks if the provided `_postId` is within the range of existing posts. If the post does not exist  
  (i.e., the `_postId` is out of range), it reverts the transaction with an error message.
  - `Post storage post = posts[_postId];`: Retrieves the post from the `posts` array based on the provided `_postId` and stores it in a `Post` storage variable named `post`.
  - `post.likes++;`: Increments the `likes` counter of the liked post by one.
  - `emit PostLiked(msg.sender, _postId)`: Emits an event `PostLiked`, indicating that the user (`msg.sender`) has liked the post specified by `_postId`.
  
  ### Adding comments to posts
  ```solidity
     function addComment(uint256 _postId, string memory _content) external onlyRegisteredUser {
        require(_postId < posts.length, "Post does not exist");
        require(bytes(_content).length > 0, "Comment should not be empty");

        uint256 commentId = postCommentsCount[_postId];
        postComments[_postId][commentId] = Comment({
            commenter: msg.sender,
            content: _content,
            timestamp: block.timestamp
        });

        postCommentsCount[_postId]++;
        posts[_postId].commentsCount++;

        emit CommentAdded(msg.sender, _postId, _content, block.timestamp);
    }
  ```
### Explanation of `addComment` functions
  This function ensures that registered users can add comments to posts and emit an event upon successful comment addition. 
  Also, it enforces security by restricting access to registered users only and ensuring that the specified post exists before allowing a comment action.

- `onlyRegisteredUser`: This modifier ensures that only registered users can add comments. It's assumed to be defined elsewhere in the contract.
- `require(_postId < posts.length, "Post does not exist")`: Ensures that the provided `_postId` corresponds to an existing post in the `posts` array.
- `require(bytes(_content).length > 0, "Comment should not be empty")`: Ensures that the content of the comment is not empty.
- `uint256 commentId = postCommentsCount[_postId];`: Retrieves the next available comment ID for the given post.
- `postComments[_postId][commentId] = Comment({ ... });`: Adds a new comment to the `postComments` mapping for the specified post ID and comment ID. It creates a new `Comment` struct with the following parameters:
  - `commenter`: The address of the user adding the comment (`msg.sender`).
  - `content`: The content of the comment, provided as input to the function (`_content`).
  - `timestamp`: The current block timestamp, indicating when the comment was added (`block.timestamp`).
- `postCommentsCount[_postId]++;`: Increments the count of comments for the specified post ID.
- `posts[_postId].commentsCount++;`: Increments the comments count in the corresponding post struct.
- `emit CommentAdded(msg.sender, _postId, _content, block.timestamp)`: Emits an event `CommentAdded`, indicating that a new comment has been added to a post. It includes the address of the commenter (`msg.sender`), the post ID (`_postId`), the content of the comment (`_content`), and the timestamp when the comment was added (`block.timestamp`).


## Viewing Posts and Comments
  ### Retrieving post
  ```solidity
   function getPost(uint256 _postId) external view returns (
        address author,
        string memory content,
        uint256 timestamp,
        uint256 likes,
        uint256 commentsCount
    ) {
        require(_postId < posts.length, "Post does not exist");
        Post memory post = posts[_postId];
        return (post.author, post.content, post.timestamp, post.likes, post.commentsCount);
    }
  ```
### Explanation of the `getPost` function
This function provides a convenient way for users to retrieve essential information about posts on the platform. It verifies that the specified post exists before returning its information.

- `_postId`: The index of the post to retrieve from the `posts` array.
- `author`: The address of the author of the post.
- `content`: The content of the post.
- `timestamp`: The timestamp when the post was created.
- `likes`: The number of likes on the post.
- `commentsCount`: The number of comments on the post.
- `require(_postId < posts.length, "Post does not exist")`: Ensures that the provided `_postId` corresponds to an existing post in the `posts` array.
- `Post memory post = posts[_postId];`: Retrieves the post struct from the `posts` array at the specified index `_postId`.
- `return (post.author, post.content, post.timestamp, post.likes, post.commentsCount);`: Returns the attributes of the post as a tuple containing the author's address, the content of the post, the timestamp, the number of likes, and the number of comments.


### Retrieving comments
```solidity
function getComment(uint256 _postId, uint256 _commentId) external view returns (
        address commenter,
        string memory content,
        uint256 timestamp
    ) {
        require(_postId < posts.length, "Post does not exist");
        require(_commentId < postCommentsCount[_postId], "Comment does not exist");

        Comment memory comment = postComments[_postId][_commentId];
        return (comment.commenter, comment.content, comment.timestamp);
    }
```

### Explanation of the `getComment` function
This function provides an easy way for users to retrieve essential information about comments on posts. It enforces security by verifying that both the specified post and comment exist before returning the comment's information.

- `_postId`: The index of the post to which the comment belongs.
- `_commentId`: The index of the comment within the specified post.
- `commenter`: The address of the commenter.
- `content`: The content of the comment.
- `timestamp`: The timestamp when the comment was created.
- `require(_postId < posts.length, "Post does not exist")`: Ensures that the provided `_postId` corresponds to an existing post in the `posts` array.
- `require(_commentId < postCommentsCount[_postId], "Comment does not exist")`: Ensures that the provided `_commentId` corresponds to an existing comment for the given post.
- `Comment memory comment = postComments[_postId][_commentId];`: Retrieves the comment struct from the `postComments` mapping for the specified post ID and comment ID.
- `return (comment.commenter, comment.content, comment.timestamp);`: Returns the attributes of the comment as a tuple containing the commenter's address, the content of the comment, and the timestamp when it was created.


### Retrieving total posts count
```solidity
 function getPostsCount() external view returns (uint256) {
        return posts.length;
    }
```
### Explanation of `getPostsCount()` function
This function provides a simple and efficient way for users to query the total number of posts on the platform. It is useful for displaying statistics about the platform's activity and engagement.

## Complete Code
This is the complete code for this decentralized social contract.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity 0.8.0;

contract SocialMedia {
    address public owner;

    struct User {
        string username;
        address userAddress;
        bool isRegistered;
    }

    mapping(address => User) public users;

    struct Post {
        address author;
        string content;
        uint256 timestamp;
        uint256 likes;
        uint256 commentsCount;
    }

    struct Comment {
        address commenter;
        string content;
        uint256 timestamp;
    }

    mapping(uint256 => mapping(uint256 => Comment)) public postComments;
    mapping(uint256 => uint256) public postCommentsCount;

    Post[] public posts;

    event UserRegistered(address indexed userAddress, string username);
    event PostCreated(address indexed author, string content, uint256 timestamp);
    event PostLiked(address indexed liker, uint256 indexed postId);
    event CommentAdded(address indexed commenter, uint256 indexed postId, string content, uint256 timestamp);

    modifier onlyRegisteredUser() {
        require(users[msg.sender].isRegistered, "User is not registered");
        _;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can call this function");
        _;
    }

    constructor() {
        owner = msg.sender;
    }

    function registerUser(string memory _username) external {
        require(!users[msg.sender].isRegistered, "User is already registered");
        require(bytes(_username).length > 0, "Username should not be empty");

        users[msg.sender] = User({
            username: _username,
            userAddress: msg.sender,
            isRegistered: true
        });

        emit UserRegistered(msg.sender, _username);
    }

    function createPost(string memory _content) external onlyRegisteredUser {
        require(bytes(_content).length > 0, "Content should not be empty");

        posts.push(Post({
            author: msg.sender,
            content: _content,
            timestamp: block.timestamp,
            likes: 0,
            commentsCount: 0
        }));

        emit PostCreated(msg.sender, _content, block.timestamp);
    }

    function likePost(uint256 _postId) external onlyRegisteredUser {
        require(_postId < posts.length, "Post does not exist");

        Post storage post = posts[_postId];
        post.likes++;

        emit PostLiked(msg.sender, _postId);
    }

    function addComment(uint256 _postId, string memory _content) external onlyRegisteredUser {
        require(_postId < posts.length, "Post does not exist");
        require(bytes(_content).length > 0, "Comment should not be empty");

        uint256 commentId = postCommentsCount[_postId];
        postComments[_postId][commentId] = Comment({
            commenter: msg.sender,
            content: _content,
            timestamp: block.timestamp
        });

        postCommentsCount[_postId]++;
        posts[_postId].commentsCount++;

        emit CommentAdded(msg.sender, _postId, _content, block.timestamp);
    }

    function getPostsCount() external view returns (uint256) {
        return posts.length;
    }

    function getPost(uint256 _postId) external view returns (
        address author,
        string memory content,
        uint256 timestamp,
        uint256 likes,
        uint256 commentsCount
    ) {
        require(_postId < posts.length, "Post does not exist");
        Post memory post = posts[_postId];
        return (post.author, post.content, post.timestamp, post.likes, post.commentsCount);
    }

    function getComment(uint256 _postId, uint256 _commentId) external view returns (
        address commenter,
        string memory content,
        uint256 timestamp
    ) {
        require(_postId < posts.length, "Post does not exist");
        require(_commentId < postCommentsCount[_postId], "Comment does not exist");

        Comment memory comment = postComments[_postId][_commentId];
        return (comment.commenter, comment.content, comment.timestamp);
    }
}

```
## Setting up Remix for CELO Alfajores
  
  ### Open Remix IDE and paste code
  Go to your web browser and open [remix IDE](https://remix.ethereum.org). You should see a similar interface like below :
  
  ![f-remix](https://github.com/fourWayz/decentralized-social/assets/157867069/ce46c1b2-5f84-4f26-b0fb-1bac53640f22)
  
  Click on `contracts` folder and create a new file named `Social.sol` then paste the full code in the file.
  
![f-remix-code](https://github.com/fourWayz/decentralized-social/assets/157867069/e6ae3c60-9207-4c43-bc56-d848f4f2a661)

### Compile and deploy
On the left tab, click on `compile` icon.
![f-remix-code-1](https://github.com/fourWayz/decentralized-social/assets/157867069/a44e20e4-6dbb-4a9d-a5d0-9cb726fdda19)

Next, click on `compile Social.sol` to compile your contract.
![f-compile](https://github.com/fourWayz/decentralized-social/assets/157867069/1b408f4f-3e06-41ec-b107-93ba1bb0aae6)

Upon successful compilation of your contracts, you should see a green checkmark like below :
![f-compiled](https://github.com/fourWayz/decentralized-social/assets/157867069/a3ff4f17-6fd8-462d-9a79-3cc6a74425d8)

Now, proceed to deploy your contract by clicking on `deploy and run transactions` icon on the left side bar.
![f-deploy](https://github.com/fourWayz/decentralized-social/assets/157867069/60a7c45d-2f76-4846-ae7b-785a55f9f80d)

Before you proceed, ensure you have connected Metamask or the provider you are using to CELO alfajores. You can follow this [guide](https://medium.com/defi-for-the-people/how-to-set-up-metamask-with-celo-912d698fcafe) to connect CELO to Metamask.

Next, select `injected provider ` under your environments to connect to Metamask and click on `deploy` button. 

Upon successful deployment of your contract, your interface should look like below :

![f-deployed](https://github.com/fourWayz/decentralized-social/assets/157867069/a4a90af2-d10b-4903-a4bb-0a6a7094e6ff)

Congratulations! You have successfully written a decentralized social media platform smart contract and deployed it to CELO on Remix

