---
id: book-club
title: Book Club - PostgreSQL 14 Internals
desc: ''
updated: 1707817386259
created: 1706541552953
---

![](/assets/images/postgresql-14-internals.png)

We are going to cover <a href="https://postgrespro.com/community/books/internals" target="_blank">PostgreSQL 14 Internals</a> book by Egor Rogov.

We will read **one chapter a week** and discuss over a Google Group. Your email will be public if you post, but otherwise it will not be visible to anyone.

## Sign up

**If you'd like to join the group, please fill this <a href="https://forms.gle/evaBqMwLX6pnw8qm6" target="_blank">Google form</a>, or reach out to me on <a href="https://twitter.com/AkJn99" target="_blank">Twitter</a>.**

Please spread the word to anyone who might be interested! :)

Also, you don't need to be an expert to join along. We are all in it to learn together as a community!

## Medium of discussion

All discussions will be done over text emails. We don't plan on having any Zoom or Google Meet calls. This allows people to read at their own pace asynchronously, and still be able to participate in the discussion.

## How to get the book

The PDF version of the book is available for free <a href="https://edu.postgrespro.com/postgresql_internals-14_en.pdf" target="_blank">here</a>. You can also order a printed copy from <a href="https://postgrespro.com/community/books/internals" target="_blank">here</a>.

## Weekly Chapter Leaders

We will have one person lead the discussion each week. They will send out an email by Saturday of that respective week recapping the chapter and sharing their thoughts. It doesn't have to be exhaustive, could even be a couple of paragraphs - the main intent is to get the discussion going and give others a starting point to chime in.

I'd love to see people interested in volunteering to lead a chapter. But if there aren't any leaders for a week, I'll take up this responsibility and get the discussion going.

To the leaders: You don't have to worry about being an expert, or about what to say. It's a community discussion, and we're all in it to learn from each other! :)

## Discussion Schedule

<table>
  <thead>
    <tr>
        <td><b>Date</b></td>
        <td><b>Leader</b></td>
        <td><b>Chapter</b></td>
        <td><b>Title</b></td>
    </tr>
  </thead>
  <tbody>
    <tr><td>March 10th, 2024</td><td>Akshat Jain</td><td>1</td><td>Introduction</td></tr>
    <tr><th colspan="4">Part I Isolation and MVCC</th></tr>
    <tr><td>March 17th, 2024</td><td>Aman Vats</td><td>2</td><td>Isolation</td></tr>
    <tr><td>March 24th, 2024</td><td>Abhijith M</td><td>3</td><td>Pages and Tuples</td></tr>
    <tr><td>March 31st, 2024</td><td>Pankaj Vatwani</td><td>4</td><td>Snapshots</td></tr>
    <tr><td>April 7th, 2024</td><td>Akshat Jaimini</td><td>5</td><td>Page Pruning and HOT Updates</td></tr>
    <tr><td>April 14th, 2024</td><td>Varun Upadhyay</td><td>6</td><td>Vacuum and Autovacuum</td></tr>
    <tr><td>April 21st, 2024</td><td>Dhananjay Naik</td><td>7</td><td>Freezing</td></tr>
    <tr><td>April 28th, 2024</td><td>Sandeep Pandey</td><td>8</td><td>Rebuilding Tables and Indexes</td></tr>
    <tr><th colspan="4">Part II Buffer Cache and WAL</th></tr>
    <tr><td>May 5th, 2024</td><td>Maneesh Chauhan</td><td>9</td><td>Buffer Cache</td></tr>
    <tr><td>May 12th, 2024</td><td>Garren Smith</td><td>10</td><td>Write-Ahead Log</td></tr>
    <tr><td>May 19th, 2024</td><td>Pratyush Singhal</td><td>11</td><td>WAL Modes</td></tr>
    <tr><th colspan="4">Part III Locks</th></tr>
    <tr><td>May 26th, 2024</td><td>Alex O</td><td>12</td><td>Relation-Level Locks</td></tr>
    <tr><td>June 2nd, 2024</td><td>Tanmay Madaan</td><td>13</td><td>Row-Level Locks</td></tr>
    <tr><td>June 9th, 2024</td><td>Ritwiz Sinha</td><td>14</td><td>Miscellaneous Locks</td></tr>
    <tr><td>June 16th, 2024</td><td>Debojit Dutta</td><td>15</td><td>Locks on Memory Structures</td></tr>
    <tr><th colspan="4">Part IV Query Execution</th></tr>
    <tr><td>June 23rd, 2024</td><td>Hemant Singh</td><td>16</td><td>Query Execution Stages</td></tr>
    <tr><td>June 30th, 2024</td><td>Raghunath Dhandapani</td><td>17</td><td>Statistics</td></tr>
    <tr><td>July 7th, 2024</td><td>Raghunath Dhandapani</td><td>18</td><td>Table Access Methods</td></tr>
    <tr><td>July 14th, 2024</td><td>Bhaskar Metiya</td><td>19</td><td>Index Access Methods</td></tr>
    <tr><td>July 21st, 2024</td><td>Garren Smith</td><td>20</td><td>Index Scans</td></tr>
    <tr><td>July 28th, 2024</td><td>Raghunath Dhandapani</td><td>21</td><td>Nested Loop</td></tr>
    <tr><td>August 4th, 2024</td><td>Apoorv Singh</td><td>22</td><td>Hashing</td></tr>
    <tr><td>August 11th, 2024</td><td>Anuttam Anand</td><td>23</td><td>Sorting and Merging</td></tr>
    <tr><th colspan="4">Part V Types of Indexes</th></tr>
    <tr><td>August 18th, 2024</td><td>Sameer Raj</td><td>24</td><td>Hash</td></tr>
    <tr><td>August 25th, 2024</td><td>Achyuth Reddy</td><td>25</td><td>B-tree</td></tr>
    <tr><td>September 1st, 2024</td><td>Naman Modi</td><td>26</td><td>GiST</td></tr>
    <tr><td>September 8th, 2024</td><td>Lasse Jacobs</td><td>27</td><td>SP-GiST</td></tr>
    <tr><td>September 15th, 2024</td><td>Sreeram Venkitesh</td><td>28</td><td>GIN</td></tr>
    <tr><td>September 22nd, 2024</td><td>Diego</td><td>29</td><td>BRIN</td></tr>
  </tbody>
</table>

