# A Step-by-Step Guide to Building a Decentralized Social Media Platform on CELO Alfajores

## Table of Contents:

1. [Introduction](#introduction)
2. [Smart Contract Design](#smart-contract-design)
3. [User Registration](#user-registration)
4. [Creating Posts](#creating-posts)
5. [Interacting with Posts](#interacting-with-posts)
6. [Viewing Posts and Comments](#viewing-posts-and-comments)
7. [Setting up Remix for CELO Alfajores](#setting-up-remix-for-celo-alfajores)
8. [Conclusion](#conclusion)

## Introduction
In this article, you will learn how to develop a decentralized social media platform. Leveraging the power of most powerful smart contracts language, Solidity and the user-friendly Remix IDE, we will explore the process of developing, and deploying this blockchain-based social media ecosystem. 

Throughout this guide, we will give a deep dive into the fundamental aspects writing smart contract. We will handle user registration, post creation, interaction mechanisms such as liking and commenting, and finally, setting up Remix for deployment on the CELO Alfajores network.

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
  ```
  
  The `SocialMedia` contract facilitates interactions on the decentralized social media platform. It stores user information, posts, and comments.
  The `User` struct represents registered users on the platform, containing fields for username, address, and registration status.
  The `Post` struct encapsulates information about each post, including the author's address, content, timestamp, number of likes, and an array of comments.
  The `Comment` struct represents individual comments, containing fields for the commenter's address, content, and timestamp.
  The `postComments` mapping is used to store comments for each post where the first key is the post's index, and the second key is the comment's index.
  The `postCommentsCount` mapping keeps track of the number of comments for each post.
  The `posts` array stores instances of the Post struct, representing individual posts on the decentralized social media platform.
  
### Description of events and modifiers

## User Registration
   - Implementation details of user registration functionality
   - Explanation of the `registerUser` function

## Creating Posts
   - Implementation details of creating posts
   - Description of the `createPost` function

## Interacting with Posts
   - Liking posts
   - Adding comments to posts
   - Explanation of the `likePost` and `addComment` functions

## Viewing Posts and Comments
   - Retrieving posts and comments
   - Explanation of the `getPost` and `getComment` functions

## Setting up Remix for CELO Alfajores
   - Introduction to Remix IDE
   - Connecting Remix to the CELO Alfajores network
   - Deploying smart contracts on CELO Alfajores using Remix

## Conclusion
   - Summary of key points covered in the article
   - Final thoughts on deploying decentralized applications on CELO Alfajores using Remix
