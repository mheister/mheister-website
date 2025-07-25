+++
title = "Learning How to Learn"
[taxonomies]
tags = ["learning", "chemistry"]
+++


<style type="text/css" media="screen">
 html {
    scroll-padding-top: 5rem;
 }
 .scroll-container {
     display: flex;
     flex: 1;
     overflow-x: auto;
     scroll-snap-type: x mandatory;
     scroll-behavior: smooth;
     backdrop-filter: blur(2px) saturate(150%);
     background-color: rgba(255, 255, 255, 1); /* transparent */
     position: relative;
 }
 .fullscreen-button {
    position: absolute;
    bottom: 20px;
    right: 20px;
    z-index: 100;
    padding: 8px 12px;
    background-color: rgba(68, 88, 128, 0.7);
    color: white;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    box-sizing: border-box;
 }
 .fullscreen-button:hover {
    background-color: rgba(68, 88, 128, 0.9);
 }
 .scroll-section {
     padding-left: 20px;
     padding-right: 20px;
     flex: 0 0 calc(100% - 40px);
     scroll-snap-align: start;
     min-height: 800px;
 }
 .scroll-container:fullscreen .scroll-section {
    min-height: 100%;
 }
 .progressbar {
     width: calc(100% + 40px);
     height: 22px;
     background-color: rgba(68, 88, 128, 0.2);
     position: relative;
     left: -20px;
     color: #eee;
 }
 .progressbar-section {
     height: 100%;
     display: block;
     text-align: right;
 }
 .progressbar-section:hover {
     height: 100%;
     background-color: rgba(68, 88, 128, 0.7);
 }
 .progressbar-section-visited {
     background-color: rgba(68, 88, 128, 0.5);
     text-align: right;
 }
 .question-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    margin-bottom: 20px;
 }
 .question-carousel {
    border: 1px solid #ddd;
    padding: 10px;
    gap: 10px;
 }
 .question-carousel .question {
    border: none;
    padding: 0;
 }
 .question {
    display: grid;
    grid-template-columns: 10fr 13fr;
    gap: 10px;
    padding: 10px;
    border: 1px solid #ddd;
 }
 .scroll-container p {
     margin-bottom: revert;
 }
 .question-carousel-nav {
     text-align: center;
     width: 100%;
     margin-top: auto;
     border-top: 1px solid #ccc;
 }
 .question-carousel-nav a {
     margin: 0 1em;
 }
</style>
<style type="text/css" media="(prefers-color-scheme: light)">
 .scroll-container {
     box-shadow: rgba(100, 100, 120, 0.2) -7px -7px 29px 0px;
 }
 .scroll-section:nth-child(odd) {
    background-color: rgba(68, 88, 128, 0.33) !important;
 }
 .section-link {
     font-size: 18px;
 }
</style>
<style type="text/css" media="(prefers-color-scheme: dark)">
 .scroll-container {
     box-shadow: rgba(232, 230, 230, 0.3) -3px -3px 29px 2px;
     background-color: rgba(30, 30, 35, 0.5) !important;
 }
 .scroll-section:nth-child(odd) {
     background-color: rgba(100, 120, 160, 0.3) !important;
 }
</style>

Learning is important. And there is science around *how* to learn successfully. Quite an
amount of science which is why I appreciate the course by Terry Sejnowski and Barbara
Oakley titled *Learning How to Learn* <sup>[1]</sup>. It condenses a lot of science into
practical advice.

<!-- more -->

As a submission for the final project of the course, I've written a small intro to an
unrelated topic with bits of the information on *how to learn* interleaved. That unrelated
topic is the basics of naming organic compounds according to the rules of the
International Union of Pure and Applied Chemistry (IUPAC). I did my best to verify the
content. Nevertheless I cannot guarantee correctness, I am by no means a chemist.

<div class="scroll-container" id="scroll-container">
{{ sidescroll_section(idx=1, total=9, content="lhtl-content/01.mdplain") }}
{{ sidescroll_section(idx=2, total=9, content="lhtl-content/02.mdplain") }}
{{ sidescroll_section(idx=3, total=9, content="lhtl-content/03.mdplain") }}
{{ sidescroll_section(idx=4, total=9, content="lhtl-content/04.mdplain") }}
{{ sidescroll_section(idx=5, total=9, content="lhtl-content/05.mdplain") }}
{{ sidescroll_section(idx=6, total=9, content="lhtl-content/06.mdplain") }}
{{ sidescroll_section(idx=7, total=9, content="lhtl-content/07.mdplain") }}
{{ sidescroll_section(idx=8, total=9, content="lhtl-content/08.mdplain") }}
{{ sidescroll_section(idx=9, total=9, content="lhtl-content/09.mdplain") }}
</div>

<script>
    let sections;

    function showSection(n) { // n is 1-based
        if (!sections) return;
        sections.forEach((section, i) => {
            section.style.display = (i === n - 1) ? 'block' : 'none';
        });
        if (window.location.hash !== '#section' + n) {
            history.pushState(null, null, '#section' + n);
        }
    }

    function initQuestionCarousel(container) {
        const questions = container.querySelectorAll('.question');
        if (questions.length <= 1) {
            return;
        }

        container.classList.add('question-carousel');
        container.dataset.currentQuestion = 1;

        questions.forEach((q, i) => {
            if (i > 0) {
                q.style.display = 'none';
            }
        });

        const nav = document.createElement('div');
        nav.className = 'question-carousel-nav';

        const prevLink = document.createElement('a');
        prevLink.href = '#';
        prevLink.innerHTML = '‹ Prev';
        prevLink.onclick = (e) => {
            e.preventDefault();
            let currentIndex = parseInt(container.dataset.currentQuestion, 10);
            if (currentIndex > 1) {
                questions[currentIndex - 1].style.display = 'none';
                currentIndex--;
                questions[currentIndex - 1].style.display = 'grid';
                container.dataset.currentQuestion = currentIndex;
                progressSpan.textContent = `${currentIndex} / ${questions.length}`;
            }
        };

        const nextLink = document.createElement('a');
        nextLink.href = '#';
        nextLink.innerHTML = 'Next ›';
        nextLink.onclick = (e) => {
            e.preventDefault();
            let currentIndex = parseInt(container.dataset.currentQuestion, 10);
            if (currentIndex < questions.length) {
                questions[currentIndex - 1].style.display = 'none';
                currentIndex++;
                questions[currentIndex - 1].style.display = 'grid';
                container.dataset.currentQuestion = currentIndex;
                progressSpan.textContent = `${currentIndex} / ${questions.length}`;
            }
        };

        const progressSpan = document.createElement('span');
        progressSpan.textContent = `1 / ${questions.length}`;

        nav.appendChild(prevLink);
        nav.appendChild(progressSpan);
        nav.appendChild(nextLink);

        container.appendChild(nav);
    }

    document.addEventListener('DOMContentLoaded', () => {
        const container = document.querySelector('.scroll-container');
        if (!container) return;

        const requestFS = container.requestFullscreen || container.webkitRequestFullscreen;
        const exitFS = document.exitFullscreen || document.webkitExitFullscreen;
        const isFullscreen = () => document.fullscreenElement || document.webkitFullscreenElement;

        if (requestFS && exitFS) {
            const button = document.createElement('button');
            button.className = 'fullscreen-button';
            button.textContent = 'Fullscreen';

            button.addEventListener('click', () => {
                if (isFullscreen()) {
                    exitFS.call(document);
                } else {
                    requestFS.call(container);
                }
            });

            const onFullscreenChange = () => {
                button.textContent = isFullscreen() ? 'Exit' : 'Fullscreen';
            };

            document.addEventListener('fullscreenchange', onFullscreenChange);
            document.addEventListener('webkitfullscreenchange', onFullscreenChange);

            container.appendChild(button);
        }

        sections = document.querySelectorAll('.scroll-section');
        const totalSections = sections.length;
        let startSection = 1;
        const hash = window.location.hash;
        if (hash.startsWith('#section')) {
            const sectionNumber = parseInt(hash.substring(8), 10);
            if (!isNaN(sectionNumber) && sectionNumber > 0 && sectionNumber <= totalSections) {
                startSection = sectionNumber;
            }
        }
        showSection(startSection);

        window.addEventListener('popstate', () => {
            const hash = window.location.hash;
            let startSection = 1;
            if (hash.startsWith('#section')) {
                const sectionNumber = parseInt(hash.substring(8), 10);
                if (!isNaN(sectionNumber) && sectionNumber > 0 && sectionNumber <= totalSections) {
                    startSection = sectionNumber;
                }
            }
            showSection(startSection);
        });

        document.querySelectorAll('.question-container').forEach(initQuestionCarousel);
    });
</script>

### See also
 - [1] [Learning How to Learn on Coursera](https://www.coursera.org/learn/learning-how-to-learn)
 - [2] [Carbon Bonding (lumen
   learning)](https://courses.lumenlearning.com/wm-nmbiology1/chapter/carbon-and-carbon-bonding)
 - [3] [Organic compound (wikipedia)](https://en.wikipedia.org/wiki/Organic_compound)
 - [4] [The Basics of Organic Nomenclature (The Crash Course - Organic
   Chemistry)](https://thecrashcourse.com/courses/the-basics-of-organic-nomenclature-crash-course-organic-chemistry-2)
 - [5] [Retrieval Practice Produces More Learning than Elaborative Studying with Concept
   Mapping](https://www.science.org/doi/abs/10.1126/science.1199327)
