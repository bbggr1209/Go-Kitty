![cat1](https://github.com/bbggr1209/Go-Kitty/assets/144644560/6a8ed237-b4c2-44b6-acf0-2fb107b7f994) ![plant_dog](https://github.com/bbggr1209/Go-Kitty/assets/144644560/91cd022b-2b9e-4d56-9ba7-7371a51c27ef)
# Go Kitty

Go Kitty는 고양이가 장애물(Plant Dog)을 피하며 점프하는 간단한 게임입니다. 스페이스바를 눌러 게임을 시작하고, 고양이가 장애물을 피하면서 점수를 획득합니다.


## 게임 방법

1. **게임 시작**: 스페이스바를 눌러 게임을 시작합니다.
2. **고양이 점프**: 스페이스바를 눌러 고양이가 점프하여 장애물을 피하도록 합니다.
3. **게임 종료**: 고양이가 장애물에 부딪히면 게임이 종료됩니다.
4. **다시 시작**: 게임 종료 후 스페이스바를 누르면 게임이 다시 시작됩니다.
5. **점수 획득**: 점수는 초단위로 환산됩니다. 오래버틸 수록 높은 점수를 획득합니다.

## 주요 기능
1. 고양이 캐릭터의 점프 및 이동
2. 장애물 생성 및 이동
3. 충돌 감지 및 게임 종료
4. 점수 계산 및 표시

## 코드 설명

### 고양이 및 장애물 초기 위치 설정
```python
    # 고양이 및 장애물 초기 위치 설정
    cat_height = imgCat1.get_size()[1]
    cat_bottom = MAX_HEIGHT - cat_height
    cat_x = 50
    cat_y = cat_bottom
    cat_width = imgCat1.get_size()[0]
    
    plant_dog_height = imgPlantDog.get_size()[1]
    plant_dog_width = imgPlantDog.get_size()[0]
    plant_dog_x = MAX_WIDTH
    plant_dog_y = MAX_HEIGHT - plant_dog_height
```
- 고양이와 장애물의 초기 위치 및 크기를 설정합니다.
- 고양이는 화면의 왼쪽 아래에 위치하며, 장애물은 화면의 오른쪽에 위치합니다.

### 게임 상태 변수 설정
```python
    # 게임 상태 변수
    game_active = False
    game_over = False
    leg_swap = True
    is_go_up = False
    jump_speed = 10
    fall_speed = 5

    game_start_time = pygame.time.get_ticks()
```
- `game_active`: 게임이 진행 중인지 여부를 나타냅니다.
- `game_over`: 게임 오버 상태를 나타냅니다.
- `leg_swap`: 고양이의 다리 이미지 토글 여부를 나타냅니다.
- `is_go_up`: 고양이가 점프 중인지 여부를 나타냅니다.
- `jump_speed`: 고양이의 점프 속도를 설정합니다.
- `fall_speed`: 고양이의 낙하 속도를 설정합니다.
- `game_start_time`: 게임 시작 시간을 기록합니다.

### 게임 루프
```python
    # 게임 루프
    while True:
        # 이벤트 처리
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    if game_over:
                        game_over = False  # 게임 오버 상태 초기화
                        game_active = False  # 게임을 시작 대기 상태로 변경
                        cat_y = cat_bottom  # 위치 초기화
                        plant_dog_x = MAX_WIDTH  # 장애물 위치 초기화
                    elif not game_active:
                        game_active = True  # 게임 시작
                        game_start_time = pygame.time.get_ticks()  # 게임 시작 시간 설정
                    if not is_go_up and cat_y == cat_bottom:
                        is_go_up = True  # 점프 시작
```
- `while True` 루프를 사용하여 게임이 계속 실행되도록 합니다.
- `pygame.event.get()`을 사용하여 이벤트를 처리합니다.
- `pygame.QUIT` 이벤트가 발생하면 게임을 종료합니다.
- `pygame.KEYDOWN` 이벤트에서 스페이스바를 누르면 게임 상태를 변경하거나 점프를 시작합니다.

### 게임 상태에 따른 화면 처리
```python
        # 게임 상태에 따른 화면 처리
        if not game_active and not game_over:  # 게임 시작 대기 화면
            screen.fill((255, 255, 255))
            title_surface = font.render('Press Space to Start', True, (0, 0, 0))
            title_x = (MAX_WIDTH - title_surface.get_width()) // 2
            title_y = (MAX_HEIGHT - title_surface.get_height()) // 2
            screen.blit(title_surface, (title_x, title_y))
        elif game_active and not game_over:  # 게임 진행 중
            current_time = pygame.time.get_ticks()
            score = (current_time - game_start_time) // 1000  # 점수 계산

            screen.fill((255, 255, 255))

            # 점프 처리
            if is_go_up:
                if cat_y > cat_bottom - 100:  # 점프 높이 조절
                    cat_y -= jump_speed
                else:
                    is_go_up = False
            elif cat_y < cat_bottom:
                cat_y += fall_speed
            else:
                cat_y = cat_bottom  # 점프 종료 및 초기 위치 복귀

            # 장애물 이동 및 그리기
            plant_dog_x -= 12.0
            if plant_dog_x <= -plant_dog_width:
                plant_dog_x = MAX_WIDTH
            screen.blit(imgPlantDog, (plant_dog_x, plant_dog_y))

            # 충돌 체크
            cat_rect = pygame.Rect(cat_x, cat_y, cat_width, cat_height)
            plant_dog_rect = pygame.Rect(plant_dog_x, plant_dog_y, plant_dog_width, plant_dog_height)
            if cat_rect.colliderect(plant_dog_rect):
                game_over = True

            # 고양이 이미지 토글 및 화면 갱신
            if not is_go_up:  # 점프 중이 아닐 때만 다리 이미지 토글
                screen.blit(imgCat1 if leg_swap else imgCat2, (cat_x, cat_y))
                leg_swap = not leg_swap
            else:
                screen.blit(imgCat1, (cat_x, cat_y))  # 점프 중에는 cat1 이미지 사용

            # 점수 표시
            score_surface = font.render(f'Score: {score}', True, (0, 0, 0))
            screen.blit(score_surface, (10, 10))
        elif game_over:  # 게임 오버 화면
            screen.fill((255, 255, 255))
            game_over_surface = font.render(f'Game Over! Score: {score} - Press Space to Restart', True, (0, 0, 0))
            go_x = (MAX_WIDTH - game_over_surface.get_width()) // 2
            go_y = (MAX_HEIGHT - game_over_surface.get_height()) // 2
            screen.blit(game_over_surface, (go_x, go_y))

        pygame.display.update()
        fps.tick(30)
```
- 게임 상태에 따라 화면을 업데이트합니다.
- 게임 시작 대기 화면, 게임 진행 중, 게임 오버 화면을 처리합니다.
- 점프, 장애물 이동, 충돌 체크, 고양이 이미지 토글, 점수 표시 등의 기능을 포함합니다.
