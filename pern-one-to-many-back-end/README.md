# 13 PERN Stack: One to Many - Part 1

**Note:** due to the size of this README.md, there is a README2.md that continues this lesson. It is in this folder/linked at the bottom.

## Adding a Second Model

### Intro & Considerations

So far, we've just worked with one model. When you get to a professional project, there will be many models. There are many considerations when adding models, including relationships and whether full CRUD is needed.

**For example:**

- A weather station reporting to a central server/database - we likely don't want to be able to edit or delete the data coming in, so we probably would not build out full CRUD in a user interface
- An online store - it's essential to retrieve the index and show pages as quickly as possible since that is most of the db calls users will make. Updates and deletes of the products don't have to be as fast, so you may design how you build the **r**ead routes differently than **u**pdate and **d**elete
- If you have students in a class, but the class is canceled, you don't want to delete the students from the database, even though **one** class will have **many** students
- In contrast, if you have **many** reviews for **one** product, if the product is deleted from the database, you would likely want to delete all the reviews that went with it because the reviews don't make sense without the product

Luckily for us, we are just building simple apps to learn. We do not yet have to consider what happens if we have thousands or millions of users. We're going to focus on syntax and code examples that are simple, so you can have a solid foundation to build on so that when you encounter new challenges, you can build on your foundation.

It's imperative not to try to optimize things too quickly. This is because:

- it takes longer (possibly much, much longer) than a simple solution
- it may be overly complex
- it may solve use cases you actually won't have

You should strive for iterations of your apps:

- First, do it
- Then do it right
- Then do it better
- Addy Osmani
