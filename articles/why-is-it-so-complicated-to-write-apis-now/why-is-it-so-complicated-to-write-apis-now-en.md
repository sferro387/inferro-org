## Why Is It So Complicated to Create APIs in the Modern World – Tech Bureaucracy
--> [RU](why-is-it-so-complicated-to-write-apis-now-ru.md) <--

As of this moment, it's July 2, 2024. I've been building websites since 2006 and still do to this day. I started with the simplest HTML pages hosted directly on an Apache server, and now everything spins in the cloud, deploys automatically through a stack, and hosts in ways I honestly can't even understand. I can only guess and trust the provided documentation.

Let me start from the end. To write a simple GET API for JSON files in 2024 at Amazon took me almost a month. A month of work included planning, creating the API contract, describing it in Smithy (what the hell is that, and why is it needed?), a couple of weeks of pushing pull requests (which Amazon calls CR, why the hell??) from one developer to another with comments. Eventually, I merged it a couple of weeks after creating the request on the fourth revision. Before this, I had a maximum of three revisions at Amazon – a new low. Why the hell we need Smithy, I don't know. Finally, I proceeded to develop the API itself. It’s just a REST GET request to the server with one parameter. This GET request should output, in the format specified in Smithy (yeah, when we already have JSON), the content of a file that is in JSON format in the AppConfig service, selected according to the parameter given in the API.

In layman's terms, it's a script that serves files from a server. That's it. I wrote the API itself in about 15 minutes. Because I had to spend an hour reading the AppConfig documentation and finding the API specification for it. The code lines, well, 200 for the API itself.

In 2006, an Apache server could read and serve a file in 3-4 lines, 10 max if it was from a database.

But hell, I wrote it, wrote tests to achieve 100% coverage, and it turned into 500 additional lines. Then there are libraries, new parameters in the stack, and the stack itself.

The stack is written in internal CDK on TypeScript. It’s something between Terraform and scripts used by developers in 2009 for using AWS services. CDK is used for creating deployments in AWS. It’s divided into many components. The key components are called stacks. In stacks, you can describe anything, even send someone to hell, but usually, they create databases, Lambdas, CloudFormations, and whatever you want. In my case, the API was designed as a Lambda function. I added the Lambda and started looking at the specification which required parameters to connect Lambda to AppConfig. To my dismay, AppConfig was declared in another stack. Not as a separate stack, but as a subordinate part of another service stack.

When I saw this, rage overcame me. I had already spent three days setting up the stack on my AWS account for testing. And now I couldn’t get the parameters needed to read files from AppConfig from another stack except by rewriting someone else’s stack and extracting AppConfig into a new stack and making it a shared structure.

Phew, I thought, I need to take a walk... grab the Husky Sausages and just walk to stop thinking about this shitty job. A bit of a side note, to speed up work, I often ask AI to do something for me. It helps me get into the work mode because AI does some things so poorly that I get upset and start redoing it. Similarly here, after the walk, I decided to give AI a chance. Tried 5-6 times, and it turned out terribly. The editor was complaining about the code, and I had to rewrite it. But I did get some small generated snippets.

After playing with the code generator, I needed to talk to the team and convince them that we needed a new stack. This is the main problem – nobody likes to rewrite what already works, and as a result, my work will be blown out of proportion. But that's only half the trouble. All existing stacks will need to be updated with careful deletion; otherwise, the deployment simply won’t work. And this will have to be done for everyone, for all environments, all developers, and their accounts. Fine, I thought, I wrote it. Surprisingly, it was quickly accepted by everyone. It even surprised everyone that it wasn’t already a separate stack, and someone is likely to get in trouble.

Work began in earnest. Creating the new stack, I shared parameters with my Lambda and the old Lambda, now simplified. It was nice to see cleaner code. Wrote tests and the new stack, probably 300-400 lines. Started testing and of course, deployment problems even to my work account. A couple of hours of deletions and redeploys – hooray, it appeared and worked.

Started testing the API, uploading configuration files, the part that creates AppConfig doesn’t work, it worked before I started doing my part. Going through everything in my head that I could have touched during development, I realized that I didn’t touch any of that, it was someone else. Started looking at the latest changes and simultaneously wrote in the chat asking what happened. Eventually found out someone added extra filters on the configuration uploads, and at the same time, a message came from the chat that everything had changed while I was developing the API. Well, at least it works, I thought, and after tweaking the files for the filters, I uploaded the configurations. They appeared!!! HURRAY!

Meanwhile, a week of developing a simple GET request had passed. I tested it and it worked. Created a pull request (or CR in Amazon lingo), and bam – conflicts. Another hour of resolving them, more tests, uploading again. Hurray, it went through but the build failed, library dependencies not loaded into the internal Artifactory, damn it. A few more builds and everything was green. Updated the comment – sent it for review.

I want to say, are you guys insane over there? Not only does calling one API cost a cent, but the whole team spent several tens of thousands of dollars developing this API. And it never needs scaling horizontally or vertically. There’s barely any traffic. Why is it so complicated, and why does nobody understand the technology and just slap things together? The world took a wrong turn with cloud technologies. So many resources are wasted. Even never mind that. A simple Nginx server on a dedicated machine could handle it infinitely faster. Development wouldn’t have taken so much time, let alone money.

It just shouldn't be this way... and AI won't help here, most of creating this API involved talking to people who know nothing about the places they shouldn’t occupy.

In the end, the full request is about 1200 lines. This is just wrong. These technologies in this format are not needed. This is tech bureaucracy.

--> [RU](why-is-it-so-complicated-to-write-apis-now-ru.md) <--
