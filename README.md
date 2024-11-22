<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Country Quiz</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background-color: #282c34;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            overflow: hidden;
        }

        #game-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            width: 100%;
            max-width: 1200px;
            height: 100%;
            padding: 20px;
            box-sizing: border-box;
        }

        #score-container {
            display: flex;
            justify-content: center;
            gap: 50px;
            width: 100%;
            color: #fff;
            margin-bottom: 20px;
        }

        #score, #best-score {
            font-size: 2.5em;
            margin: 0;
        }

        #country-container {
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            width: 100%;
            gap: 5%;
        }

        .country {
            display: flex;
            flex-direction: row;
            align-items: center;
            padding: 20px;
            cursor: pointer;
            transition: transform 0.3s, background-color 0.3s;
            background-color: #20232a;
            border: 2px solid #61dafb;
            border-radius: 15px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            width: 45%;
            height: 150px; /* Increased height to ensure cards are taller than flags */
            box-sizing: border-box;
        }

        .country:hover {
            transform: scale(1.05);
            background-color: #282c34;
        }

        .flag {
            width: 120px;
            height: auto; /* Ensures the flag maintains its aspect ratio */
            margin-right: 30px; /* Added spacing between the flag and the text */
            border-radius: 5px;
        }

        .country-name {
            font-size: 2em;
            font-weight: bold;
            text-align: left;
        }

        #question {
            font-size: 2.5em;
            margin: 20px;
            text-align: center;
            color: #61dafb;
            text-shadow: 2px 2px 4px #000;
        }

        #game-over {
            display: none;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.8);
            color: #fff;
            font-size: 2.5em;
            z-index: 10;
        }

        #game-over span {
            margin-bottom: 20px;
        }

        #restart-game-over-button {
            padding: 15px 30px;
            font-size: 1.2em;
            cursor: pointer;
            border: none;
            border-radius: 10px;
            background-color: #61dafb;
            color: #282c34;
            transition: background-color 0.3s;
        }

        #restart-game-over-button:hover {
            background-color: #21a1f1;
        }

        @keyframes slideInLeft {
            from {
                transform: translateX(-100%);
            }
            to {
                transform: translateX(0);
            }
        }

        @keyframes slideOutLeft {
            from {
                transform: translateX(0);
            }
            to {
                transform: translateX(-100%);
            }
        }

        @keyframes slideInRight {
            from {
                transform: translateX(100%);
            }
            to {
                transform: translateX(0);
            }
        }

        @keyframes slideOutRight {
            from {
                transform: translateX(0);
            }
            to {
                transform: translateX(100%);
            }
        }

        .slide-in-left {
            animation: slideInLeft 0.5s ease-out forwards;
        }

        .slide-out-left {
            animation: slideOutLeft 0.5s ease-out forwards;
        }

        .slide-in-right {
            animation: slideInRight 0.5s ease-out forwards;
        }

        .slide-out-right {
            animation: slideOutRight 0.5s ease-out forwards;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="score-container">
            <span id="score">Score: <span id="score-value">0</span></span>
            <span id="best-score">Best Score: <span id="best-score-value">0</span></span>
        </div>
        <div id="country-container">
            <div id="country1" class="country slide-in-left">
                <img class="flag" src="" alt="Flag">
                <div class="country-name"></div>
            </div>
            <div id="country2" class="country slide-in-right">
                <img class="flag" src="" alt="Flag">
                <div class="country-name"></div>
            </div>
        </div>
        <div id="question">Which country has a higher GDP?</div>
    </div>
    <div id="game-over">
        <span>Game Over</span>
        <span id="final-score"></span>
        <span id="best-score-text"></span>
        <button id="restart-game-over-button">Restart</button>
    </div>
    <script>
        const countryQuiz = {
            countriesData: [
                { name: 'USA', flag: 'https://flagcdn.com/us.svg', gdp: 23320000, population: 331002651, landSize: 9833517, forests: 3100000, gdpPerCapita: 64742, lifeExpectancy: 79, literacyRate: 99, coastline: 19924, medianAge: 38.5, hdi: 0.926, olympicMedals: 2980, agriculturalLand: 4087436, billionaires: 724, populationDensity: 36, touristsPerYear: 79600000, militaryBudget: 732000000000 },
                { name: 'China', flag: 'https://flagcdn.com/cn.svg', gdp: 17730000, population: 1444216107, landSize: 9596961, forests: 2200000, gdpPerCapita: 9946, lifeExpectancy: 77, literacyRate: 96, coastline: 14500, medianAge: 38.4, hdi: 0.761, olympicMedals: 634, agriculturalLand: 5257000, billionaires: 698, populationDensity: 150, touristsPerYear: 65700000, militaryBudget: 261000000000 },
                { name: 'India', flag: 'https://flagcdn.com/in.svg', gdp: 3390000, population: 1380004385, landSize: 3287263, forests: 710000, gdpPerCapita: 2100, lifeExpectancy: 69, literacyRate: 74, coastline: 7517, medianAge: 28.4, hdi: 0.645, olympicMedals: 35, agriculturalLand: 1797150, billionaires: 166, populationDensity: 420, touristsPerYear: 17900000, militaryBudget: 71000000000 },
                { name: 'Japan', flag: 'https://flagcdn.com/jp.svg', gdp: 4230000, population: 126476461, landSize: 377975, forests: 250000, gdpPerCapita: 40250, lifeExpectancy: 84, literacyRate: 100, coastline: 29751, medianAge: 48.6, hdi: 0.919, olympicMedals: 439, agriculturalLand: 45660, billionaires: 50, populationDensity: 334, touristsPerYear: 31900000, militaryBudget: 49000000000 },
                { name: 'Germany', flag: 'https://flagcdn.com/de.svg', gdp: 4080000, population: 83783942, landSize: 357022, forests: 114000, gdpPerCapita: 45923, lifeExpectancy: 81, literacyRate: 99, coastline: 2389, medianAge: 45.7, hdi: 0.947, olympicMedals: 1361, agriculturalLand: 170104, billionaires: 136, populationDensity: 234, touristsPerYear: 39400000, militaryBudget: 52900000000 },
                { name: 'UK', flag: 'https://flagcdn.com/gb.svg', gdp: 3070000, population: 67886011, landSize: 243610, forests: 31000, gdpPerCapita: 41489, lifeExpectancy: 81, literacyRate: 99, coastline: 12429, medianAge: 40.5, hdi: 0.932, olympicMedals: 883, agriculturalLand: 170000, billionaires: 171, populationDensity: 279, touristsPerYear: 37900000, militaryBudget: 59000000000 },
                { name: 'France', flag: 'https://flagcdn.com/fr.svg', gdp: 2940000, population: 65273511, landSize: 551695, forests: 170000, gdpPerCapita: 41463, lifeExpectancy: 83, literacyRate: 99, coastline: 3427, medianAge: 41.7, hdi: 0.901, olympicMedals: 798, agriculturalLand: 294000, billionaires: 40, populationDensity: 119, touristsPerYear: 89400000, militaryBudget: 50700000000 },
                { name: 'Italy', flag: 'https://flagcdn.com/it.svg', gdp: 2100000, population: 60461826, landSize: 301340, forests: 110000, gdpPerCapita: 33100, lifeExpectancy: 83, literacyRate: 99, coastline: 7600, medianAge: 47.3, hdi: 0.892, olympicMedals: 742, agriculturalLand: 140000, billionaires: 45, populationDensity: 201, touristsPerYear: 58100000, militaryBudget: 27600000000 },
                { name: 'Canada', flag: 'https://flagcdn.com/ca.svg', gdp: 2140000, population: 37742154, landSize: 9984670, forests: 3400000, gdpPerCapita: 43547, lifeExpectancy: 82, literacyRate: 99, coastline: 202080, medianAge: 42.2, hdi: 0.929, olympicMedals: 625, agriculturalLand: 678500, billionaires: 47, populationDensity: 4, touristsPerYear: 22100000, militaryBudget: 22200000000 },
                { name: 'Russia', flag: 'https://flagcdn.com/ru.svg', gdp: 1780000, population: 145912025, landSize: 17098246, forests: 8000000, gdpPerCapita: 11600, lifeExpectancy: 73, literacyRate: 100, coastline: 37653, medianAge: 39.6, hdi: 0.824, olympicMedals: 547, agriculturalLand: 1230000, billionaires: 101, populationDensity: 9, touristsPerYear: 24600000, militaryBudget: 61000000000 },
                { name: 'Brazil', flag: 'https://flagcdn.com/br.svg', gdp: 2050000, population: 212559417, landSize: 8515767, forests: 4900000, gdpPerCapita: 6788, lifeExpectancy: 75, literacyRate: 93, coastline: 7491, medianAge: 33.5, hdi: 0.765, olympicMedals: 150, agriculturalLand: 2630000, billionaires: 65, populationDensity: 25, touristsPerYear: 6580000, militaryBudget: 27000000000 },
                { name: 'Australia', flag: 'https://flagcdn.com/au.svg', gdp: 1700000, population: 25687041, landSize: 7692024, forests: 1250000, gdpPerCapita: 54157, lifeExpectancy: 83, literacyRate: 99, coastline: 25760, medianAge: 38.7, hdi: 0.944, olympicMedals: 547, agriculturalLand: 410000, billionaires: 39, populationDensity: 3, touristsPerYear: 9610000, militaryBudget: 32000000000 },
                { name: 'South Korea', flag: 'https://flagcdn.com/kr.svg', gdp: 1800000, population: 51269185, landSize: 100210, forests: 64000, gdpPerCapita: 32000, lifeExpectancy: 82, literacyRate: 99, coastline: 2413, medianAge: 43.2, hdi: 0.916, olympicMedals: 287, agriculturalLand: 17670, billionaires: 28, populationDensity: 512, touristsPerYear: 17500000, militaryBudget: 45300000000 },
                { name: 'Mexico', flag: 'https://flagcdn.com/mx.svg', gdp: 1320000, population: 128932753, landSize: 1964375, forests: 640000, gdpPerCapita: 9882, lifeExpectancy: 75, literacyRate: 95, coastline: 9330, medianAge: 29.3, hdi: 0.779, olympicMedals: 69, agriculturalLand: 973920, billionaires: 12, populationDensity: 66, touristsPerYear: 45000000, militaryBudget: 11000000000 },
                { name: 'Spain', flag: 'https://flagcdn.com/es.svg', gdp: 1490000, population: 46754778, landSize: 505992, forests: 180000, gdpPerCapita: 27000, lifeExpectancy: 83, literacyRate: 98, coastline: 4964, medianAge: 43.6, hdi: 0.904, olympicMedals: 155, agriculturalLand: 272000, billionaires: 24, populationDensity: 92, touristsPerYear: 83000000, militaryBudget: 17900000000 },
                { name: 'Saudi Arabia', flag: 'https://flagcdn.com/sa.svg', gdp: 880000, population: 34813871, landSize: 2149690, forests: 0, gdpPerCapita: 23000, lifeExpectancy: 75, literacyRate: 95, coastline: 2640, medianAge: 30.8, hdi: 0.854, olympicMedals: 3, agriculturalLand: 172000, billionaires: 10, populationDensity: 16, touristsPerYear: 20000000, militaryBudget: 67000000000 },
                { name: 'Turkey', flag: 'https://flagcdn.com/tr.svg', gdp: 850000, population: 84339067, landSize: 783562, forests: 226000, gdpPerCapita: 10000, lifeExpectancy: 76, literacyRate: 96, coastline: 7200, medianAge: 32.2, hdi: 0.820, olympicMedals: 104, agriculturalLand: 236000, billionaires: 27, populationDensity: 108, touristsPerYear: 45000000, militaryBudget: 20000000000 },
                { name: 'South Africa', flag: 'https://flagcdn.com/za.svg', gdp: 335000, population: 59308690, landSize: 1221037, forests: 40723, gdpPerCapita: 6000, lifeExpectancy: 64, literacyRate: 95, coastline: 2798, medianAge: 27.6, hdi: 0.705, olympicMedals: 89, agriculturalLand: 799400, billionaires: 4, populationDensity: 49, touristsPerYear: 10000000, militaryBudget: 4500000000 },
                { name: 'Argentina', flag: 'https://flagcdn.com/ar.svg', gdp: 450000, population: 45195777, landSize: 2780400, forests: 946000, gdpPerCapita: 10000, lifeExpectancy: 76, literacyRate: 99, coastline: 4989, medianAge: 31.6, hdi: 0.845, olympicMedals: 74, agriculturalLand: 1450000, billionaires: 6, populationDensity: 16, touristsPerYear: 7500000, militaryBudget: 3700000000 },
                { name: 'Nigeria', flag: 'https://flagcdn.com/ng.svg', gdp: 450000, population: 206139589, landSize: 923768, forests: 103000, gdpPerCapita: 2200, lifeExpectancy: 55, literacyRate: 62, coastline: 853, medianAge: 18.1, hdi: 0.534, olympicMedals: 25, agriculturalLand: 738920, billionaires: 3, populationDensity: 223, touristsPerYear: 400000, militaryBudget: 2000000000 },
                { name: 'Indonesia', flag: 'https://flagcdn.com/id.svg', gdp: 1141000, population: 273523621, landSize: 1904569, forests: 920000, gdpPerCapita: 4185, lifeExpectancy: 71, literacyRate: 95, coastline: 54716, medianAge: 30.2, hdi: 0.718, olympicMedals: 32, agriculturalLand: 570000, billionaires: 21, populationDensity: 142, touristsPerYear: 16000000, militaryBudget: 9000000000 },
                { name: 'Israel', flag: 'https://flagcdn.com/il.svg', gdp: 482000, population: 9216900, landSize: 20770, forests: 32000, gdpPerCapita: 51800, lifeExpectancy: 83, literacyRate: 97, coastline: 273, medianAge: 30.5, hdi: 0.919, olympicMedals: 13, agriculturalLand: 4300, billionaires: 21, populationDensity: 413, touristsPerYear: 4500000, militaryBudget: 18000000000 },
                { name: 'Malaysia', flag: 'https://flagcdn.com/my.svg', gdp: 365000, population: 32780000, landSize: 330803, forests: 188000, gdpPerCapita: 11150, lifeExpectancy: 76, literacyRate: 95, coastline: 4675, medianAge: 29.2, hdi: 0.802, olympicMedals: 13, agriculturalLand: 72682, billionaires: 14, populationDensity: 97, touristsPerYear: 25900000, militaryBudget: 3800000000 },
                { name: 'Thailand', flag: 'https://flagcdn.com/th.svg', gdp: 500000, population: 69799978, landSize: 513120, forests: 220000, gdpPerCapita: 7200, lifeExpectancy: 76, literacyRate: 94, coastline: 3219, medianAge: 39, hdi: 0.755, olympicMedals: 35, agriculturalLand: 191607, billionaires: 28, populationDensity: 136, touristsPerYear: 39700000, militaryBudget: 5700000000 },
                { name: 'Egypt', flag: 'https://flagcdn.com/eg.svg', gdp: 363000, population: 102334404, landSize: 1002450, forests: 2000, gdpPerCapita: 3530, lifeExpectancy: 71, literacyRate: 75, coastline: 2450, medianAge: 24.6, hdi: 0.707, olympicMedals: 38, agriculturalLand: 36731, billionaires: 6, populationDensity: 100, touristsPerYear: 11700000, militaryBudget: 4400000000 },
                { name: 'Philippines', flag: 'https://flagcdn.com/ph.svg', gdp: 402000, population: 109581085, landSize: 300000, forests: 210000, gdpPerCapita: 3679, lifeExpectancy: 71, literacyRate: 96, coastline: 36289, medianAge: 25.7, hdi: 0.718, olympicMedals: 14, agriculturalLand: 114000, billionaires: 15, populationDensity: 363, touristsPerYear: 8200000, militaryBudget: 4100000000 },
                { name: 'Vietnam', flag: 'https://flagcdn.com/vn.svg', gdp: 343000, population: 97338579, landSize: 331210, forests: 140000, gdpPerCapita: 3537, lifeExpectancy: 75, literacyRate: 95, coastline: 3444, medianAge: 31.9, hdi: 0.704, olympicMedals: 6, agriculturalLand: 108000, billionaires: 8, populationDensity: 293, touristsPerYear: 18000000, militaryBudget: 5200000000 },
                { name: 'Colombia', flag: 'https://flagcdn.com/co.svg', gdp: 336000, population: 50882891, landSize: 1141748, forests: 605000, gdpPerCapita: 6566, lifeExpectancy: 77, literacyRate: 95, coastline: 3208, medianAge: 31, hdi: 0.767, olympicMedals: 32, agriculturalLand: 400000, billionaires: 6, populationDensity: 42, touristsPerYear: 4700000, militaryBudget: 9800000000 }
            ],
            currentQuestionIndex: 0,
            score: 0,
            bestScore: 0,
            currentQuestion: null,
            usedQuestions: [],

            questionTypes: [
                { text: 'Which country has a higher GDP?', compare: 'gdp' },
                { text: 'Which country has a higher population?', compare: 'population' },
                { text: 'Which country has a larger land size?', compare: 'landSize' },
                { text: 'Which country has more forests?', compare: 'forests' },
                { text: 'Which country has a higher GDP per capita?', compare: 'gdpPerCapita' },
                { text: 'Which country has a higher life expectancy?', compare: 'lifeExpectancy' },
                { text: 'Which country has a higher literacy rate?', compare: 'literacyRate' },
                { text: 'Which country has a longer coastline?', compare: 'coastline' },
                { text: 'Which country has a higher median age?', compare: 'medianAge' },
                { text: 'Which country has a higher HDI?', compare: 'hdi' },
                { text: 'Which country has won more Olympic medals?', compare: 'olympicMedals' },
                { text: 'Which country has a larger agricultural land area?', compare: 'agriculturalLand' },
                { text: 'Which country has more billionaires?', compare: 'billionaires' },
                { text: 'Which country has a higher population density?', compare: 'populationDensity' },
                { text: 'Which country has a higher number of tourists per year?', compare: 'touristsPerYear' },
                { text: 'Which country has a larger military budget?', compare: 'militaryBudget' }
            ],

            init: function() {
                this.cacheDom();
                this.bindEvents();
                this.loadBestScore();
                this.renderNewQuestion();
            },

            cacheDom: function() {
                this.gameContainer = document.getElementById('game-container');
                this.scoreContainer = document.getElementById('score-container');
                this.scoreDisplay = document.getElementById('score-value');
                this.bestScoreDisplay = document.getElementById('best-score-value');
                this.countryContainer = document.getElementById('country-container');
                this.country1 = document.getElementById('country1');
                this.country2 = document.getElementById('country2');
                this.questionDisplay = document.getElementById('question');
                this.gameOverScreen = document.getElementById('game-over');
                this.finalScoreDisplay = document.getElementById('final-score');
                this.bestScoreText = document.getElementById('best-score-text');
                this.restartGameOverButton = document.getElementById('restart-game-over-button');
            },

            bindEvents: function() {
                this.country1.addEventListener('click', this.checkAnswer.bind(this, 'country1'));
                this.country2.addEventListener('click', this.checkAnswer.bind(this, 'country2'));
                this.restartGameOverButton.addEventListener('click', this.restartGame.bind(this));
            },

            loadBestScore: function() {
                const bestScore = localStorage.getItem('bestScore');
                this.bestScore = bestScore ? parseInt(bestScore) : 0;
                this.bestScoreDisplay.textContent = this.bestScore;
            },

            saveBestScore: function() {
                localStorage.setItem('bestScore', this.bestScore);
            },

            renderNewQuestion: function() {
                this.currentQuestion = this.getRandomQuestion();
                this.questionDisplay.textContent = this.currentQuestion.text;
                this.currentCountries = this.getRandomCountries();
                this.renderCountries();
            },

            getRandomQuestion: function() {
                let randomIndex;
                do {
                    randomIndex = Math.floor(Math.random() * this.questionTypes.length);
                } while (this.usedQuestions.includes(randomIndex) && this.usedQuestions.length < this.questionTypes.length);

                if (this.usedQuestions.length === this.questionTypes.length) {
                    this.usedQuestions = [];
                }

                this.usedQuestions.push(randomIndex);
                return this.questionTypes[randomIndex];
            },

            getRandomCountries: function() {
                let country1Index, country2Index;
                do {
                    country1Index = Math.floor(Math.random() * this.countriesData.length);
                    country2Index = Math.floor(Math.random() * this.countriesData.length);
                } while (country1Index === country2Index);
                return [this.countriesData[country1Index], this.countriesData[country2Index]];
            },

            renderCountries: function() {
                this.country1.querySelector('.flag').src = this.currentCountries[0].flag;
                this.country1.querySelector('.country-name').textContent = this.currentCountries[0].name;
                this.country2.querySelector('.flag').src = this.currentCountries[1].flag;
                this.country2.querySelector('.country-name').textContent = this.currentCountries[1].name;

                this.country1.classList.remove('slide-out-left', 'slide-in-left');
                this.country2.classList.remove('slide-out-right', 'slide-in-right');

                requestAnimationFrame(() => {
                    this.country1.classList.add('slide-in-left');
                    this.country2.classList.add('slide-in-right');
                });
            },

            checkAnswer: function(selectedCountry) {
                const selectedCountryData = this.currentCountries[selectedCountry === 'country1' ? 0 : 1];
                const otherCountryData = this.currentCountries[selectedCountry === 'country1' ? 1 : 0];
                const compareProperty = this.currentQuestion.compare;

                if (selectedCountryData[compareProperty] >= otherCountryData[compareProperty]) {
                    this.handleCorrectAnswer();
                } else {
                    this.handleIncorrectAnswer();
                }
            },

            handleCorrectAnswer: function() {
                this.score++;
                this.scoreDisplay.textContent = this.score;

                this.country1.classList.add('slide-out-left');
                this.country2.classList.add('slide-out-right');

                setTimeout(() => {
                    this.renderNewQuestion();
                }, 500);
            },

            handleIncorrectAnswer: function() {
                if (this.score > this.bestScore) {
                    this.bestScore = this.score;
                    this.saveBestScore();
                }

                this.finalScoreDisplay.textContent = `Final Score: ${this.score}`;
                this.bestScoreText.textContent = `Best Score: ${this.bestScore}`;
                this.gameOverScreen.style.display = 'flex';
            },

            restartGame: function() {
                this.score = 0;
                this.scoreDisplay.textContent = this.score;
                this.gameOverScreen.style.display = 'none';
                this.renderNewQuestion();
            }
        };

        countryQuiz.init();
    </script>
</body>
</html>
